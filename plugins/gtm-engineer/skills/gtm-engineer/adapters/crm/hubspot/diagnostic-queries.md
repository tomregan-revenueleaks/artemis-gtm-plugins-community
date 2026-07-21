# HubSpot Diagnostic Queries (connect-and-measure, read-only)

The connect-and-measure library for HubSpot. When a buyer's HubSpot is connected, this file tells the diagnostic engine, per revenue leak, the exact read to run: which object, which filter, which window, and what to compute from the result. Every figure that comes back is labeled **measured** (the query is named and the record count stated), never confused with a benchmark or a buyer's remembered number.

HubSpot is the Phase 1 flagship platform, so this library is complete for every leak the CRM can actually see. The leaks the CRM cannot see (tool spend, website content, anonymous traffic volume) are named at the bottom as paste-back, stated plainly, so "every number carries its provenance" is never breached by pretending the CRM knows something it does not.

## Read-only doctrine

Diagnostics never write. The only HubSpot write tool, `manage_crm_objects`, is never called during a diagnostic. Every query below uses one of these verified read tools:

| Tool | Used for |
|---|---|
| `search_crm_objects` | count and filter records; the returned `total` is the measured count |
| `query_crm_data` | SQL-style aggregates (COUNT, MEDIAN, GROUP BY, DATE_TRUNC) over one object type |
| `search_properties` | detect whether a portal-specific property exists (scoring, qualification, Warmly fields) |
| `get_properties` | read enumeration options for a discovered property |
| `search_owners` | resolve owner IDs for ownerless-record and stale-owner checks |

**Verified against the loaded HubSpot MCP tool schemas on 2026-07-01.** Re-verify on MCP version bumps per the Capability Verification Protocol. Two surface facts every query below respects:

- `query_crm_data` is one object type per query. No JOIN, no subquery, no DISTINCT, no aliases, no CASE, no string functions, no HAVING, and no arithmetic across two columns. Cross-object reads use `OBJECT.property` (for example `SELECT COMPANY.icp_segment FROM DEAL`), at most two associated object types, and `associations.OBJECT` only in WHERE for IS NULL / IS NOT NULL existence checks. You must call `tool_guidance` before the first `query_crm_data` call in a session.
- `search_crm_objects` returns a `total` count for any filter; that count is the measured number. Filters within a group are AND, groups are OR, max 5 groups and 18 filters. Currency amounts require the matching currency-code property alongside them.

## Provenance and property discovery

Two property classes appear below. **Standard HubSpot properties** (`createdate`, `hs_lifecyclestage_*_date`, `lifecyclestage`, `hs_analytics_source`, `hs_is_closed_won`, `hs_is_closed`, `days_to_close`, `notes_last_contacted`, `hs_last_activity_date`, `hs_lastmodifieddate`, `hubspot_owner_id`, `domain`, `numberofemployees`, `industry`) can be read directly. **Buyer-custom properties** (the scoring, qualification, and Warmly or RB2B fields, carried by the field registry where your install includes a build module and named inline here regardless) exist only if the buyer built them, so their queries open with a `search_properties` existence check; absence is itself a measured finding (the structural leak is confirmed).

Windows below use concrete example dates anchored near the engagement (a trailing 90-day window shown as `2026-04-01` to `2026-06-30`, a half-year cohort as `2026-01-01` to `2026-06-30`); swap in the buyer's actual window at run time. `query_crm_data` takes date strings, not timestamps.

---

## Leak 1: Slow Lead Response (speed-to-lead gap)

**What it measures:** the median time from lead creation to first sales touch, and whether inbound leads sit untouched. It does not measure anonymous-visitor response, only the response to records that reached the CRM.

**Reads:**

1. Confirm the portal's first-touch property:
 - `search_properties` objectType `CONTACT`, keywords `["hs_time_to_first_engagement","notes_first_contacted","notes_last_contacted","hs_sales_email_last_replied"]`.
2. If a stored first-engagement duration property exists, take its median by create-month:
 - `query_crm_data`: `SELECT DATE_TRUNC(createdate,'MONTH'), MEDIAN(hs_time_to_first_engagement), COUNT(*) FROM CONTACT WHERE createdate BETWEEN '2026-04-01' AND '2026-06-30' GROUP BY DATE_TRUNC(createdate,'MONTH')`.
3. If no duration property exists (the common case), pull the record-level dates and compute the delta client-side (SQL cannot subtract two columns):
 - `search_crm_objects` objectType `contacts`, filter `createdate GTE 2026-04-01` (as the epoch value the portal expects), `properties: ["createdate","notes_last_contacted","hs_sales_email_last_replied","hubspot_owner_id"]`, `sorts: createdate DESCENDING`, `limit: 200`. Compute median hours from `createdate` to the earliest logged touch across the sample; state the sample size from `total`.
4. Untouched-inbound share (structural):
 - `search_crm_objects` objectType `contacts`, filter group A: `createdate GTE 2026-04-01` AND `NOT_HAS_PROPERTY notes_last_contacted` AND `lifecyclestage IN [lead, marketingqualifiedlead]`. The returned `total` over the same-window created total is the share going cold with no recorded touch.

**Computation:** median response hours (or the untouched share) versus the sub-5-minute target. **Provenance:** measured (queries named, counts from `total` / `COUNT(*)`). Total anonymous-visitor response is not in the CRM; if the buyer wants that, it is paste-back from the visitor tool.

---

## Leak 2: Anonymous Visitors (deanonymization gap)

**What it measures:** identified-account volume and ICP-matched identification coverage. The CRM cannot see total anonymous traffic; that denominator is web analytics, labeled paste-back.

**Reads:**

1. Confirm the visitor-ID fields exist (Warmly or RB2B wired):
 - `search_properties` objectType `COMPANY`, keywords `["is_icp_visitor","last_identified_visit","identified_visit_source"]`. Absence means no deanonymization is running: the structural leak is confirmed on the spot.
2. Identified companies in the window:
 - `search_crm_objects` objectType `companies`, filter `last_identified_visit GTE 2026-04-01`. `total` is the identified-account count.
3. ICP-matched identified companies:
 - `search_crm_objects` objectType `companies`, filter `is_icp_visitor EQ true` AND `last_identified_visit GTE 2026-04-01`. `total` over the step-2 total is the ICP-match share the workflow is letting through.
4. Known-lead volume for context:
 - `query_crm_data`: `SELECT COUNT(*) FROM CONTACT WHERE createdate BETWEEN '2026-04-01' AND '2026-06-30'`.

**Computation:** identified-account count and ICP-match share (measured); recoverable-visitor and dollar math needs the total monthly visitor count, which is **paste-back** from the buyer's analytics, labeled as benchmark or buyer-input when it feeds the leak quantifier. **Provenance:** measured for the identified side; visitor total labeled paste-back.

---

## Leak 3: No Lead Scoring (fit-plus-intent prioritization gap)

**What it measures:** whether a scoring model exists and routes, and, if it does, whether the bands actually separate conversion.

**Reads:**

1. Detect a scoring model:
 - `search_properties` objectType `CONTACT`, keywords `["artemis_composite_score","artemis_score_band","hs_predictive_contact_score","lead_score"]`. No score property present confirms the structural leak.
2. If a band property exists, band distribution:
 - `query_crm_data`: `SELECT artemis_score_band, COUNT(*) FROM CONTACT WHERE createdate BETWEEN '2026-01-01' AND '2026-06-30' GROUP BY artemis_score_band`.
3. Opportunity rate by band (does a higher band convert better?):
 - Per band, `search_crm_objects` objectType `contacts`, filter group: `artemis_score_band EQ Hot` AND `num_associated_deals GTE 1`; compare each band's `total` against that band's total from step 2. If Hot does not out-convert Warm, the model is miscalibrated, a different finding from having no model at all.

**Computation:** presence or absence (structural), plus opp-rate spread across bands (measured). **Provenance:** structural (property existence) and measured (band counts). If a score exists only as a field but no routing reads it, that surfaces in Leak 10's queue checks, not here.

---

## Leak 4: Wrong-customer targeting (ICP undefined or firmographic-only)

**What it measures:** win rate and median sales cycle against the industry-by-motion benchmark; the signature of chasing the wrong accounts is a low win rate and a long cycle together.

**Reads:**

1. Win rate over closed deals in the window:
 - `query_crm_data`: `SELECT hs_is_closed_won, COUNT(*) FROM DEAL WHERE hs_is_closed = 'true' AND closedate BETWEEN '2026-01-01' AND '2026-06-30' GROUP BY hs_is_closed_won`. Win rate = won over (won + lost).
2. Median sales cycle on wins:
 - `query_crm_data`: `SELECT DATE_TRUNC(closedate,'QUARTER'), MEDIAN(days_to_close), COUNT(*) FROM DEAL WHERE hs_is_closed_won = 'true' AND closedate BETWEEN '2026-01-01' AND '2026-06-30' GROUP BY DATE_TRUNC(closedate,'QUARTER')`. `days_to_close` is a standard HubSpot deal property.
3. If the ICP scorecard exists, win rate by segment (the ICP win-rate report the field registry defines where a build module is installed, specified inline below, run live):
 - `query_crm_data`: `SELECT COMPANY.icp_segment, COUNT(*) FROM DEAL WHERE hs_is_closed_won = 'true' AND closedate BETWEEN '2026-01-01' AND '2026-06-30' GROUP BY COMPANY.icp_segment`, and the same without the won filter for the denominator. A flat win rate across Ideal and Not-ICP means the scorecard is not predicting, an ICP-definition finding.

**Computation:** win rate and median cycle (measured), graded against the benchmark table in `diagnose/benchmarks.md`. **Provenance:** measured, record counts stated.

---

## Leak 7: Outbound / sequencing gap (partial CRM visibility)

**What it measures:** meeting-booking volume and outbound-sourced pipeline share. Reply-rate and deliverability data for 1:1 sales sequences is not cleanly exposed on this surface and is owned by the Amplemarket adapter; this section measures only the CRM-visible sliver.

**Reads:**

1. Confirm the meeting engagement object is supported in this portal (object support varies): read `get_user_details` for supported object types before querying meetings.
2. Meetings booked in the window:
 - `search_crm_objects` objectType `meetings`, filter `hs_meeting_start_time BETWEEN 2026-04-01 and 2026-06-30`. `total` is the booked-meeting count; divide by rep count (from `search_owners`) for meetings-per-rep against the SLG benchmark.
3. Outbound-sourced pipeline share:
 - `query_crm_data`: `SELECT hs_analytics_source, COUNT(*) FROM CONTACT WHERE createdate BETWEEN '2026-04-01' AND '2026-06-30' GROUP BY hs_analytics_source`. The `OFFLINE` and any custom outbound source buckets over the total give the outbound share.

**Computation:** meetings-per-rep and outbound source share (measured). **Provenance:** measured for the CRM sliver; reply rate, deliverability, and sequence performance are **paste-back or Amplemarket-adapter** reads, labeled as such.

---

## Leak 6: Content / organic gap (partial CRM visibility)

**What it measures:** the share of leads and pipeline attributed to organic search. It does not measure ranking or traffic quality, which are web analytics.

**Reads:**

1. Lead source mix:
 - `query_crm_data`: `SELECT hs_analytics_source, COUNT(*) FROM CONTACT WHERE createdate BETWEEN '2026-01-01' AND '2026-06-30' GROUP BY hs_analytics_source`. `hs_analytics_source` is standard (`ORGANIC_SEARCH`, `PAID_SEARCH`, `DIRECT_TRAFFIC`, `REFERRALS`, `SOCIAL_MEDIA`, `EMAIL_MARKETING`, `OFFLINE`).
2. Organic-sourced pipeline (does organic reach revenue, not just top-of-funnel):
 - `query_crm_data`: `SELECT COMPANY.hs_analytics_source, COUNT(*) FROM DEAL WHERE createdate BETWEEN '2026-01-01' AND '2026-06-30' GROUP BY COMPANY.hs_analytics_source`.

**Computation:** organic share of leads and of pipeline (measured). **Provenance:** measured for attribution; SEO ranking, AI-citation share, and traffic volume are **paste-back** from the diagnostics suite and analytics.

---

## Leak 8: Nurture / dormant re-engagement gap

**What it measures:** the pile of open records with no recent activity that nobody is working, the clearest CRM-native leak in the set.

**Reads:**

1. Dormant contacts in an open stage:
 - `search_crm_objects` objectType `contacts`, filter group: `notes_last_contacted LTE 2026-04-01` AND `lifecyclestage IN [lead, marketingqualifiedlead, salesqualifiedlead, opportunity]`. `total` is the dormant open-contact count.
 - Distribution by stage: `query_crm_data`: `SELECT lifecyclestage, COUNT(*) FROM CONTACT WHERE notes_last_contacted < '2026-04-01' AND lifecyclestage IN ('lead','marketingqualifiedlead','salesqualifiedlead','opportunity') GROUP BY lifecyclestage`.
2. Companies with open deals but a stale last-activity clock:
 - `search_crm_objects` objectType `companies`, filter group: `num_associated_deals GTE 1` AND `hs_last_activity_date LTE 2026-04-01`.
3. If `nurture_stage` exists, dormant contacts stuck below BOFU:
 - `search_properties` objectType `CONTACT`, keywords `["nurture_stage"]`, then `query_crm_data`: `SELECT nurture_stage, COUNT(*) FROM CONTACT WHERE hs_last_activity_date < '2026-04-01' GROUP BY nurture_stage`.

**Computation:** dormant record counts by stage against the recency cutoff (measured). Revival-rate and dollar math applies the benchmark revival rate to the measured count, labeled. **Provenance:** measured, cutoff and counts stated.

---

## Leak 10: MQL to SQL qualification / handoff gap

**What it measures:** the MQL-to-SQL conversion rate and the count of MQLs that stall without ever reaching SQL, the two clearest signals of a broken handoff. HubSpot stamps a dated lifecycle property each time a contact enters a stage, so both are measured directly.

**Reads:**

1. MQL cohort in the window:
 - `query_crm_data`: `SELECT COUNT(*) FROM CONTACT WHERE hs_lifecyclestage_marketingqualifiedlead_date BETWEEN '2026-01-01' AND '2026-06-30'`.
2. Of that cohort, those that reached SQL:
 - `query_crm_data`: `SELECT COUNT(*) FROM CONTACT WHERE hs_lifecyclestage_marketingqualifiedlead_date BETWEEN '2026-01-01' AND '2026-06-30' AND hs_lifecyclestage_salesqualifiedlead_date BETWEEN '2026-01-01' AND '2026-09-30'`. Conversion = step 2 over step 1.
3. Stalled MQLs (reached MQL, never SQL, aging):
 - `search_crm_objects` objectType `contacts`, filter group: `hs_lifecyclestage_marketingqualifiedlead_date LTE 2026-04-01` AND `NOT_HAS_PROPERTY hs_lifecyclestage_salesqualifiedlead_date`. `total` is the stalled count.
4. If qualification properties exist, handoff completeness:
 - `search_properties` objectType `CONTACT`, keywords `["qualification_review_needed","eb_status","handoff_outcome"]`, then `search_crm_objects` objectType `contacts`, filter `qualification_review_needed EQ true` for the count still awaiting a human pass, and `query_crm_data`: `SELECT handoff_outcome, COUNT(*) FROM CONTACT WHERE bdr_handoff_date BETWEEN '2026-01-01' AND '2026-06-30' GROUP BY handoff_outcome` for the rejection rate (a rejection rate above the standard threshold means standards diverged).

**Computation:** MQL-to-SQL conversion rate versus the roughly 13% median, plus the stalled-MQL count (measured). **Provenance:** measured, cohort counts stated. The lifecycle-date properties are standard HubSpot.

---

## Leak 11: RevOps / data quality / CRM hygiene gap

**What it measures:** the substrate leak, duplicate accounts, ownerless records, rotting open deals, missing key fields, and competing custom fields, all of which tax every other number.

**Reads:**

1. Duplicate companies by domain:
 - `query_crm_data`: `SELECT domain, COUNT(*) FROM COMPANY WHERE domain IS NOT NULL GROUP BY domain`. Any domain with a count above 1 is a duplicate candidate (SQL has no HAVING, so filter the returned rows). This is the dedup signal the visitor-ID workflow depends on.
2. Ownerless records:
 - `search_crm_objects` objectType `contacts`, filter `NOT_HAS_PROPERTY hubspot_owner_id`; repeat for `companies` and for open `deals` (`hs_is_closed EQ false` AND `NOT_HAS_PROPERTY hubspot_owner_id`). Each `total` is an ownerless count.
3. Rotting open deals:
 - `search_crm_objects` objectType `deals`, filter group: `hs_is_closed EQ false` AND `hs_lastmodifieddate LTE 2026-04-01`. `total` is the count of open deals untouched in the window.
4. Missing key firmographics:
 - `search_crm_objects` objectType `companies`, filter `NOT_HAS_PROPERTY industry` (repeat for `numberofemployees`). `total` is the count of accounts the ICP and scoring layers cannot grade.
5. Competing fields (the "three company size fields" finding):
 - `search_properties` objectType `COMPANY`, keywords `["company_size","employees","industry","segment","revenue"]`. More than one live property for the same concept is a hygiene finding to consolidate onto the native property (the field registry sets the canonical target where a build module is installed).

**Computation:** counts of duplicates, ownerless records, rotting deals, and missing fields (measured); framed as the reliability tax on every other leak. **Provenance:** measured, counts stated.

---

## Leaks the CRM cannot measure (labeled paste-back)

Depth over breadth: naming these keeps provenance honest rather than faking a query.

- **Leak 5, Tech-stack sprawl.** Tool contracts, category overlap, and spend are not CRM data and are not exposed on this MCP surface. This is discovery-and-paste-back, owned by the stack module.
- **Leak 9, Conversion-content gap.** Hidden pricing, weak case studies, and demo-page yield are website content and web-analytics questions. The CRM shows only the downstream form-fill lead count (`query_crm_data`: `SELECT hs_analytics_source, COUNT(*) FROM CONTACT WHERE createdate BETWEEN ... GROUP BY hs_analytics_source`, form conversions); the conversion-rate denominator (site traffic) and the content quality itself are paste-back from the diagnostics suite.

For both, the connect-and-measure offer does not claim CRM measurement; the buyer pastes the numbers and every figure is labeled buyer-input accordingly.
