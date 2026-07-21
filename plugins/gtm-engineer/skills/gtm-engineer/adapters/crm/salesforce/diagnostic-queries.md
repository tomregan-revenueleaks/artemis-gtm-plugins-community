# Salesforce Diagnostic Query Library

The connect-and-measure layer for Salesforce. When a buyer's Salesforce is connected (report or SOQL-backed reads via the CRM MCP), the diagnostic engine measures the leaks from their actual records instead of asking them to paste numbers, and every figure in the report says exactly which query it came from and how many records it read.

**Read-only, always.** Every query below is a SOQL `SELECT` or a report export. No `INSERT`, `UPDATE`, `DELETE`, `upsert`, or Apex that mutates. This library runs inside the read-only-first permission boundary in `tool-connections.md`; the diagnostic never writes to the buyer's org. If a metric cannot be read read-only, it stays paste-back, stated plainly.

**These queries run against the org as it exists today, before any GTM Engineer build.** They therefore use standard Salesforce objects and fields (`Lead`, `Opportunity`, `Account`, `Contact`, `Task`, `Event`, `Campaign`, `LeadHistory`) plus a small set of buyer-named placeholders for fields the org already has. Replace the placeholders live with the buyer's real API names:

- `<MQL_STATUS>` = the Lead `Status` value the buyer calls "marketing qualified" (often `MQL` or `Working`).
- `<SCORE_FIELD>` = the buyer's existing lead-score field, if any (Pardot `Score`, a custom `Lead_Score__c`, or none).
- `<CLOSED_WON>` / `<CLOSED_LOST>` = the org's closed StageNames.

**Provenance discipline.** Every row a query returns is **measured**: the report labels it with the query name and the record count the read returned. A leak is only ever confirmed when the buyer's own real number falls below 0.7x its industry-by-motion benchmark (the confirm threshold in the diagnostic playbook). This library never restates a benchmark number; the benchmark it compares against is single-sourced in `diagnose/benchmarks.md`. A metric that has to be benchmark-filled because the org cannot produce it is "at benchmark" by definition and is never turned into a leak.

**Median caveat.** SOQL has no median aggregate. Where a leak needs a median (lead response time), the query reads the underlying rows and the engine computes the median from the exported set; the entry says so. Averages and counts use SOQL aggregates directly.

Each entry states: the leak it measures, the detection condition from the leak taxonomy it maps to, the read (read-only), the computation, and the provenance label.

---

## Leak 1: Slow Lead Response / Speed-to-Lead gap

**Measures:** median time from lead creation to first human touch, and whether inbound leads have any routing/SLA at all. Taxonomy condition: response measured in hours/days, or inbound volume with no routing.

**Read (read-only).** First-touch time has no standard field, so read lead-creation and the earliest logged activity per lead over the window, then compute the gap per lead:

```sql
-- Inbound leads created in the window, with their creation timestamp
SELECT Id, CreatedDate, LeadSource, Status
FROM Lead
WHERE CreatedDate = LAST_N_DAYS:90
 AND LeadSource IN ('Web','Inbound','Content','Demo Request') -- the buyer's inbound sources
ORDER BY CreatedDate

-- Earliest completed activity per lead (the first-touch event), read separately
SELECT WhoId, MIN(CreatedDate) firstTouch
FROM Task
WHERE WhoId != null
 AND CreatedDate = LAST_N_DAYS:90
 AND Status = 'Completed'
GROUP BY WhoId
```

**Computation.** Join the two reads on `Lead.Id = Task.WhoId`; per lead, `first_touch_gap = firstTouch - CreatedDate`; report the **median** and the share of inbound leads with **no** completed activity inside the SLA target. If no `Task`/`Event` rows exist for inbound leads at all, that is the strongest signal: there is no measurable first-touch motion. Structural cross-check for "no routing/SLA": `SELECT COUNT(Id) FROM AssignmentRule WHERE SobjectType = 'Lead' AND Active = true` returning 0 means creation-time routing is not even configured.

**Provenance.** Measured. State: leads read, leads with a first-touch activity, median gap in minutes/hours. Compare the median to the speed-to-lead benchmark in `diagnose/benchmarks.md`; below 0.7x confirms the leak.

---

## Leak 2: No Lead Scoring / no fit+intent prioritization

**Measures:** whether a usable score exists and whether high-fit leads are actually worked first. Taxonomy condition: every lead treated the same, or scoring exists at the field level but does not route, or BDRs work FIFO.

**Read (read-only).** Structural presence first:

```sql
-- Does a score field carry real spread, or is it null/flat?
SELECT COUNT(Id) total, COUNT(<SCORE_FIELD>) scored,
 MIN(<SCORE_FIELD>) minScore, MAX(<SCORE_FIELD>) maxScore, AVG(<SCORE_FIELD>) avgScore
FROM Lead
WHERE CreatedDate = LAST_N_DAYS:90
 AND Status = '<MQL_STATUS>'
```

If the org has no score field, this leak is a structural gap (skip the read, record "no score field present"). If it has one, test whether score drives work order:

```sql
-- Conversion by score band: does a higher score actually convert better?
SELECT IsConverted, COUNT(Id) leads, AVG(<SCORE_FIELD>) avgScore
FROM Lead
WHERE CreatedDate = LAST_N_DAYS:180
 AND Status = '<MQL_STATUS>'
GROUP BY IsConverted
```

**Computation.** Flat spread (min == max, or null on most rows) means no working score, a structural leak. If a score exists but converted and non-converted leads show near-identical average scores, the score is not predictive or is not being worked in order. Score present, wide spread, and converted leads clearly higher-scoring means scoring is healthy, name it as a strength, do not leak it.

**Provenance.** Measured. State leads read, % scored, score range, and the converted-vs-not average-score gap.

---

## Leak 3: Wrong-customer targeting / ICP undefined or firmographic-only

**Measures:** win rate and cycle length by segment, the signature of chasing the wrong accounts. Taxonomy condition: win rate below benchmark and cycle above benchmark together, or ICP undefined/firmographic-only.

**Read (read-only).**

```sql
-- Win rate and cycle by industry (or the buyer's segment field)
SELECT Account.Industry seg,
 COUNT(Id) opps,
 SUM(CASE WHEN StageName = '<CLOSED_WON>' THEN 1 ELSE 0 END) won,
 AVG(CASE WHEN IsClosed = true THEN (CloseDate - DAY_ONLY(CreatedDate)) ELSE null END) avgCycleDays
FROM Opportunity
WHERE CreatedDate = LAST_N_DAYS:365
GROUP BY Account.Industry
ORDER BY COUNT(Id) DESC
```

(Salesforce SOQL does not support `CASE` inside aggregate the way SQL does; where the org rejects it, run two grouped counts, one filtered `StageName = '<CLOSED_WON>'` and one unfiltered, and divide. The report builder does this natively with a summary formula, which is the cleaner path when SOQL aggregation is awkward.)

**Computation.** Per segment: `win_rate = won / opps`, `avg_cycle = avgCycleDays`. The leak is the combination: segments where win rate sits below the benchmark AND cycle sits above it are absorbing effort at poor yield, which is what "no real ICP" looks like in the pipeline. Also flag the inverse: if one or two segments carry most of the wins at a short cycle and the pipeline is nonetheless spread across many, the ICP is known in the data but not enforced in targeting.

**Provenance.** Measured. State opportunities read and per-segment win rate and cycle. Compare win rate and cycle each to their benchmarks in `diagnose/benchmarks.md`.

---

## Leak 4: Content / SEO / organic gap

**Measures:** the organic share of created pipeline. Taxonomy condition: low or no organic pipeline, or every lead is paid.

**Read (read-only).**

```sql
SELECT LeadSource, COUNT(Id) leads
FROM Lead
WHERE CreatedDate = LAST_N_DAYS:180
GROUP BY LeadSource
ORDER BY COUNT(Id) DESC
```

Pair with sourced pipeline value where opportunities carry `LeadSource`:

```sql
SELECT LeadSource, COUNT(Id) opps, SUM(Amount) pipeline
FROM Opportunity
WHERE CreatedDate = LAST_N_DAYS:365
GROUP BY LeadSource
```

**Computation.** `organic_share = organic leads / total leads` (organic = the buyer's `Organic`/`SEO`/`Content`/`Referral` sources, named live). A near-zero organic share with volume concentrated in `Paid`/`Ads` sources is the leak: they buy every lead they could earn. This is partial by nature, the demand-quality half lives outside the CRM (Ahrefs/GSC), so label the CRM read as the pipeline-share signal and note the organic-traffic side is measured in the content adapter, not here.

**Provenance.** Measured (CRM share only). State leads and opportunities read and the organic share.

---

## Leak 5: Outbound / cold email / signal / sequencing gap

**Measures:** whether outbound produces pipeline and at what activity yield. Taxonomy condition: static-list outbound with low reply rates, no signal triggers, or meetings/SDR below benchmark.

**Read (read-only).**

```sql
-- Outbound-sourced opportunities and their yield
SELECT COUNT(Id) opps, SUM(Amount) pipeline
FROM Opportunity
WHERE CreatedDate = LAST_N_DAYS:365
 AND LeadSource IN ('Outbound','SDR','Prospecting','Cold Email')

-- Outbound activity volume as the denominator
SELECT OwnerId, COUNT(Id) outboundTasks
FROM Task
WHERE CreatedDate = LAST_N_DAYS:90
 AND Type IN ('Email','Call')
 AND TaskSubtype = 'Email'
GROUP BY OwnerId
```

**Computation.** `outbound_meetings_per_rep` and `outbound_opps / outbound_activity` give the yield. Reply rate and deliverability, the sharpest outbound signals, live in the engagement tool (`adapters/tools/amplemarket/diagnostic-queries.md`), not the CRM; this Salesforce read measures the pipeline outcome and the activity denominator only. Say so, and label the reply-rate figure paste-back or Amplemarket-measured, never invented from CRM activity.

**Provenance.** Measured (pipeline + activity only). State outbound opportunities and activities read. Reply-rate provenance handed to the engagement-tool adapter.

---

## Leak 6: Nurture / dormant re-engagement gap

**Measures:** the pile of dormant records nobody works, the biggest hidden revenue source. Taxonomy condition: high lead decay, no behavior-triggered re-engagement, or dormant accounts unworked.

**Read (read-only).**

```sql
-- Dormant open leads: no activity in 6+ months, still in an active status
SELECT COUNT(Id) dormantLeads
FROM Lead
WHERE IsConverted = false
 AND Status != 'Disqualified'
 AND (LastActivityDate < LAST_N_MONTHS:6 OR LastActivityDate = null)

-- Total open leads, for the decay ratio
SELECT COUNT(Id) openLeads
FROM Lead
WHERE IsConverted = false AND Status != 'Disqualified'

-- Dormant contacts on accounts with no open opportunity
SELECT COUNT(Id) dormantContacts
FROM Contact
WHERE (LastActivityDate < LAST_N_MONTHS:6 OR LastActivityDate = null)
 AND Account.Id NOT IN (SELECT AccountId FROM Opportunity WHERE IsClosed = false)
```

**Computation.** `dormant_rate = dormantLeads / openLeads`. The recoverable-pipeline frame multiplies the dormant count by a conservative revival rate and the buyer's deal size (the dollar math is the nurture calculator's, this query supplies the dormant count as its input). A dormant rate well above the benchmark, plus no record-triggered re-engagement workflow (a structural check the MA adapter runs), confirms the leak.

**Provenance.** Measured. State dormant leads, open leads, dormant contacts, and the dormant rate.

---

## Leak 7: Conversion-content gap (pricing / demo / case study / landing)

**Measures:** the yield of the traffic and leads the buyer already has, stage by stage. Taxonomy condition: high top-of-funnel and low conversion-surface yield.

**Read (read-only).**

```sql
-- Lead-to-opportunity conversion on inbound, the surface yield
SELECT COUNT(Id) inboundLeads,
 SUM(CASE WHEN ConvertedOpportunityId != null THEN 1 ELSE 0 END) converted
FROM Lead
WHERE CreatedDate = LAST_N_DAYS:180
 AND LeadSource IN ('Web','Inbound','Content','Demo Request')
```

(Same `CASE`-in-aggregate caveat as Leak 3; use a report summary formula or two counts if the org rejects it.)

**Computation.** `inbound_lead_to_opp = converted / inboundLeads`. A healthy top-of-funnel volume with a lead-to-opp rate below benchmark points at the conversion surface (pricing, demo, case studies) wasting traffic already paid for. This is partial: page-level conversion (which page, which CTA) is a web-analytics read, not a CRM read; label the CRM figure as the funnel-yield signal and hand the page-level side to the conversion adapter.

**Provenance.** Measured (funnel yield only). State inbound leads read and the lead-to-opp rate.

---

## Leak 8: MQL to SQL qualification / handoff gap

**Measures:** where qualified demand dies between marketing and sales. Taxonomy condition: MQL-to-SQL below the median, informal handoff, or leads marked MQL that sit.

**Read (read-only).** Cohorted, per the KPI dictionary (SQLs accepted over MQLs created in the same cohort, not this-month-over-this-month):

```sql
-- MQL cohort: leads that reached the MQL status in the window
SELECT COUNT(Id) mqls,
 SUM(CASE WHEN IsConverted = true THEN 1 ELSE 0 END) converted
FROM Lead
WHERE CreatedDate = LAST_N_DAYS:180
 AND Status = '<MQL_STATUS>'

-- MQLs that sit: at MQL status with no activity in 14+ days
SELECT COUNT(Id) sittingMqls
FROM Lead
WHERE Status = '<MQL_STATUS>'
 AND IsConverted = false
 AND (LastActivityDate < LAST_N_DAYS:14 OR LastActivityDate = null)
```

If the org tracks status changes, the cleaner cohort read is `LeadHistory` where `Field = 'Status'` and `NewValue = '<MQL_STATUS>'`, which timestamps the MQL moment exactly rather than inferring it from current status.

**Computation.** `mql_to_sql = converted / mqls` on the cohort. `sitting_share = sittingMqls / current MQLs` quantifies the "marked MQL then sit" symptom independently. Both below-benchmark conversion and a high sitting share point at a broken handoff.

**Provenance.** Measured. State MQLs in cohort, conversions, sitting MQLs, and both rates. Compare `mql_to_sql` to the benchmark in `diagnose/benchmarks.md`.

---

## Leak 9: RevOps / data quality / CRM hygiene / reporting gap

**Measures:** the substrate leak, the tax on every other number. Taxonomy condition: duplicate accounts, stale owner mappings, competing field values, untrustworthy dashboards.

**Read (read-only).**

```sql
-- Duplicate accounts by website domain
SELECT Website, COUNT(Id) dupes
FROM Account
WHERE Website != null
GROUP BY Website
HAVING COUNT(Id) > 1

-- Open records owned by inactive users (stale owner mappings)
SELECT COUNT(Id) orphanOpps
FROM Opportunity
WHERE IsClosed = false AND Owner.IsActive = false

-- Field completeness on the fields the funnel depends on
SELECT COUNT(Id) total,
 COUNT(Industry) hasIndustry,
 COUNT(NumberOfEmployees) hasSize,
 COUNT(Website) hasDomain
FROM Account
WHERE CreatedDate = LAST_N_DAYS:365
```

**Computation.** `duplicate_accounts` = sum of the extra rows in the dupe read (each group of N counts N-1 duplicates). `orphaned_open_pipeline` = opps owned by inactive users, which silently misroute. `field_completeness` per field = has / total; low completeness on Industry, size, or domain means scoring and ICP will be built on sand. Dollarize as the RevOps calculator does (manual-ops hours plus a reliability discount on every downstream metric); this query supplies the hygiene counts as its inputs.

**Provenance.** Measured. State duplicate account groups, orphaned open opps, and per-field completeness percentages.

---

## Leaks this library does not measure (honest boundary)

Depth over breadth: this platform ships complete for every leak Salesforce can actually read. Two leaks are not CRM-measurable, and the connect-and-measure offer never claims otherwise for them:

- **Anonymous Visitors / no deanonymization.** The leak lives in traffic that never became a CRM record, so no Salesforce query can see it. The CRM can only supply the denominator context (identified-lead volume) against a visitor count that comes from the visitor-ID tool or paste-back. Measured in the visitor-ID diagnostic front, not here.
- **Tech-stack sprawl / redundant tools.** Contract values and category overlap live in the buyer's billing and vendor records, not the CRM. This is paste-back or the stack-grader's own inputs. A Salesforce read cannot confirm it.

For both, the report labels the figure paste-back or external-tool, never measured, and never manufactures a CRM number to fill the gap.
