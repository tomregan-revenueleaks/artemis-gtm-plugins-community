# Attio Diagnostic Queries (read-only)

This is the connect-and-measure query library for Attio. When a buyer connects their Attio workspace, the diagnostic engine measures the leaks it can from the buyer's actual records instead of asking them to paste numbers, and every figure it produces carries its provenance. This library specifies, per leak, the exact read: which object, which filter, which window, and the computation that turns records into a number.

**Everything here is read-only.** These queries never create, update, or delete a record, note, task, attribute, view, or list. The build engine's write surface is defined elsewhere and gated separately; the diagnostic never touches it. If a measurement would require a write, it is not a measurement, it is paste-back.

**The verified Attio read surface** these queries use:

| Read | MCP tool | Used for |
|---|---|---|
| List records on an object | `list-records` | population counts, filtered slices |
| Filtered record search | `search-records` | targeted slices (missing owner, stale, blank domain) |
| Fetch specific records | `get-records-by-ids` | pulling `created_at` and attribute values for a sampled set |
| List attribute definitions | `list-attribute-definitions` | structural probe: does the scoring / ICP / qualification block even exist |
| List lists / records in a list | `list-lists`, `list-records-in-list`, `list-list-attribute-definitions` | queue and segment membership |
| Aggregated report | `run-basic-report` | distributions, conversion rates, win rate by segment |
| Email activity metadata | `search-emails-by-metadata` | first-outbound-touch timestamp, logged outbound volume |
| Meeting metadata | `search-meetings` | first-touch via booked meeting |
| Task reads | `list-tasks` | first-touch via completed task, follow-up hygiene |
| Note reads | `search-notes-by-metadata` | qualification capture evidence |
| Workspace members | `list-workspace-members` | owner mapping, stale-owner detection |

Attribute and view creation are not on the verified surface and are never needed here (reads only). Attio ships fast; if a read tool's shape has drifted, find the current equivalent, the query logic does not change. Never quote Attio pricing.

---

## The provenance contract

Every figure this library produces is labeled with exactly one provenance, and the output-integrity pass records the query text behind any measured row:

- **Measured:** computed from the buyer's records. Always state the query and the record count it ran over (for example "measured over 214 People created in the last 90 days").
- **Reported:** the buyer pasted it back. Used when the fact lives outside the CRM (total website traffic, sequence send volume, tool contract values).
- **Benchmark:** a labeled substitution from `diagnose/benchmarks.md`, keyed to the buyer's industry and motion. A benchmark-filled figure is at benchmark by definition and is never itself turned into a leak.

**The structural probe comes first.** For any leak whose fix is a CRM data model (scoring, ICP, qualification), the first read is `list-attribute-definitions` on the relevant object. If the block's canonical attributes (carried by `field-registry.md` where your install includes a build module, otherwise the attribute names spelled out inline below) are absent, that absence IS the finding: the system is not wired, which is a structural leak, and no distribution query is needed to confirm it. Only when the block exists do the distribution and conversion reads run.

**Depth over breadth.** Attio is a CRM, so it can measure the funnel-and-hygiene leaks well and cannot measure website, tool-spend, or engagement-tool leaks at all. Every applicable leak below ships a complete read. The leaks a CRM genuinely cannot see are labeled paste-back plainly, never dressed up with a shallow query. When the connect-and-measure offer names Attio, it names it only for the leaks in the "measured" and "partial" sets below.

---

## Leak 1: Slow Lead Response / Speed-to-Lead gap (measured)

- **Object / window:** People (or Deals if leads land as deals) created in the last 90 days.
- **Reads:** `list-records` to enumerate the created cohort with `created_at`; for each record, the earliest outbound touch from `search-emails-by-metadata` (direction outgoing), `list-tasks` (completed touch tasks on the record), and `search-meetings` (first booked meeting). Take the minimum of those timestamps as first-touch.
- **Computation:** first-touch minus `created_at` per record; report the **median** time-to-first-touch (median, not mean, one weekend skews the mean). Segment by `routing_bucket` where present so hot inbound is measured separately from warm.
- **Grade:** compare the median against the speed-to-lead benchmark in `diagnose/benchmarks.md`. Hours or days is a confirmed leak.
- **Provenance:** measured; state the cohort count and how many had any recorded first touch (records with no touch at all are the worst slice, count them explicitly).
- **Structural fallback:** if no email/meeting/task activity syncs into Attio, first-touch is not measurable from the CRM; say so and take the median response time as reported paste-back. A workspace with routing but no SLA attribute is itself the signal.

## Leak 2: Anonymous Visitors / no deanonymization (partial: CRM measures the identified-but-unworked slice)

- **Structural probe:** `list-attribute-definitions` on Companies for `last_identified_visit` and `icp_visitor`. Absent means no visitor-ID tool is writing into the CRM, a structural leak on its own.
- **If present, object / window:** Companies with `last_identified_visit` in the last 30 days.
- **Reads:** `search-records` on Companies filtered by `last_identified_visit` within window and `icp_visitor = true`; `run-basic-report` counting those with no associated Person and no associated Deal (identified ICP accounts that were never worked).
- **Computation:** identified ICP accounts in window, and the count and share of them with zero downstream Person/Deal, the recoverable pipeline that walked out after identification.
- **Provenance:** measured for the identified-and-unworked slice (state the count). The total-visitor denominator and the identification rate are **not** in the CRM: total monthly visitors is reported paste-back or a labeled benchmark, and the anonymous-share math runs in the deanonymization calculator, not here.

## Leak 3: No Lead Scoring / no fit+intent prioritization (measured)

- **Structural probe:** `list-attribute-definitions` on People for `artemis_composite_score` and `artemis_score_band` (carried by `field-registry.md` where a build module is installed; the names are given here regardless). Absent means no scoring model exists, a structural leak; stop here, the absence is the finding.
- **If present, reads:** `run-basic-report` on People grouped by `artemis_score_band` for the band distribution; `search-records` counting People at/past MQL stage with a null `artemis_composite_score` (the unscored-but-active coverage gap).
- **Computation:** band distribution, and the share of active leads that carry no score. A scoring model that covers a fraction of the active pipeline is a partial leak.
- **Provenance:** measured; state the People count scored versus total active.
- **Note:** whether BDRs actually work in score order (adherence) is behavioral and lives in the coaching-loop paste-back, not a record read.

## Leak 4: Wrong-customer targeting / ICP undefined (measured)

- **Structural probe:** `list-attribute-definitions` on Companies for `icp_segment` and `icp_tier`. Absent means ICP is not modeled in the CRM, a structural leak.
- **If present, reads:** `run-basic-report` on Deals grouped by the company's `icp_segment`, split won versus lost, for **win rate by segment**; `run-basic-report` on open Deals summing value where `icp_segment = Not-ICP` or `icp_tier = D` for **misdirected open pipeline**; overall median sales-cycle and win rate from Deals for the "low win rate and long cycle together" signature.
- **Computation:** win rate per segment (validates the scorecard, an Ideal segment that does not win better is a broken scorecard, not just a leak), and the share of open pipeline value sitting in poor-fit accounts.
- **Provenance:** measured; state the Deal counts per segment. If segment coverage is thin (many Companies with a blank `icp_segment`), report that as a coverage caveat, do not extrapolate off a handful of scored accounts.

## Leak 5: Tech-stack sprawl / redundant tools (paste-back, not CRM-measurable)

A CRM does not know the buyer's tool contracts, categories, or spend. This leak is measured from the tool inventory and contract values the buyer pastes back, graded in the stack module. Do not attempt a proxy read; label it reported and move on.

## Leak 6: Content / SEO / organic gap (partial: only if lead source is tracked in-CRM)

- **Structural probe:** `list-attribute-definitions` on People/Companies for a lead-source attribute (`lead_source`, `source`, or the buyer's equivalent). Absent means the CRM cannot attribute origin; the whole leak is reported paste-back plus web-search readiness signals.
- **If present, reads:** `run-basic-report` grouped by the source attribute over records created in the last 90 days, for the **organic share of new records**.
- **Computation:** organic-sourced share of created leads. A near-zero organic share alongside real paid volume is the in-CRM signal of the demand-gen gap.
- **Provenance:** measured for the source distribution (state the cohort count); the SEO, ranking, and AI-citation metrics themselves are reported or measured by the content diagnostic, not the CRM.

## Leak 7: Outbound / sequencing gap (partial: engagement tool is the primary source)

- **Primary source:** reply rate, sequence sends, and deliverability live in the engagement tool. Use `adapters/tools/amplemarket/diagnostic-queries.md` for the real measurement; the CRM only cross-checks logged outcomes.
- **If email syncs into Attio, reads:** `search-emails-by-metadata` for outbound email volume and threaded replies over the last 30 days on target accounts.
- **Computation:** a coarse logged-reply share as a cross-check only. State plainly that the denominator (total sequence sends) is not in the CRM, so this figure floors the true reply rate, it does not measure it.
- **Provenance:** the CRM cross-check is measured but partial; the real reply-rate and deliverability figures are reported from the engagement tool or measured by its adapter.

## Leak 8: Nurture / dormant re-engagement gap (measured)

- **Object / window:** People (and their Companies) with a last-interaction older than 180 days, excluding closed-won and active-pipeline records.
- **Reads:** `search-records` on People filtered to `nurture_stage != Active Opportunity` and last activity (Attio native last-interaction, or `last_activity_date`) older than 180 days; `run-basic-report` for the count and the share of the total contact base. Where `nurture_stage` is not built, filter on last-interaction age alone and note the coarser cut.
- **Computation:** dormant-account count and dormant share of the database, the revivable base the buyer already paid to acquire. Split by whether any pain-mapped or high-intent signal fired since going dormant (those are the warm revival targets).
- **Provenance:** measured; state the total contact base and the dormant count. The revival rate applied to dollarize it is a labeled benchmark from `diagnose/benchmarks.md`.

## Leak 9: Conversion-content gap (paste-back, not CRM-measurable)

Pricing transparency, demo-page yield, and case-study strength are website and CRO facts, not CRM records. Measured by the conversion diagnostic from the buyer's traffic and pages; label it reported here and do not proxy it.

## Leak 10: MQL to SQL qualification / handoff gap (measured)

- **Structural probe:** `list-attribute-definitions` for the stage attribute and the qualification block (`qualification_review_needed`, `handoff_outcome`, `disqualified_reason`, carried by `field-registry.md` where a build module is installed; the names are given here regardless).
- **Reads:** `run-basic-report` on People/Deals by stage over the last 90 days for **MQL to SQL conversion** (count reaching SQL/opportunity divided by count reaching MQL); `search-records` for People at MQL stage with no owner or no activity in 14 or more days (the "marked MQL then sit" pile); `search-records` for records that exited (disqualified/recycled) with an empty `disqualified_reason`, and rejected handoffs with an empty `ae_rejection_reason` (the "exited without a reason" audit, which should trend to zero when the policy works).
- **Computation:** the MQL to SQL rate against the benchmark in `diagnose/benchmarks.md`, the count of stalled MQLs, and the count of unexplained exits.
- **Provenance:** measured; state the stage cohort counts. If stages are not modeled, this is reported paste-back and the absence of a stage model is itself the finding.

## Leak 11: RevOps / data quality / CRM hygiene gap (measured, the CRM's home turf)

Run all of these; the leak is the sum of them, and hygiene debt is the tax on every other number.

- **Blank-domain companies (dedup risk):** `search-records` on Companies where domain is empty. Domain is Attio's native match key; blanks are where duplicates breed.
- **Ownerless active records:** `search-records` on People/Deals past MQL with no `lead_owner` (or native owner). Cross-reference `list-workspace-members` to flag records owned by a deactivated member (stale-owner routing).
- **Stale records:** `run-basic-report` counting open records with no activity in 90 or more days that are not deliberately dormant per Leak 8.
- **Unresolved review flags:** `search-records` where `qualification_review_needed = true`, the backlog of AI-written fields no human has confirmed.
- **Competing fields:** `list-attribute-definitions` scanned for more than one attribute describing the same fact (multiple "company size" or "industry" style attributes, or a hand-set `icp_fit` coexisting with a derived one), the classic "three competing company size fields" defect.
- **Computation:** each count on its own, plus a hygiene rollup. Frame the dollar impact as manual-ops hours times loaded cost times 52 (paste-back the hours) plus a stated reliability discount on every other measured figure, because bad data inflates pipeline and hides leaks.
- **Provenance:** measured for every count above (state each); the hours-to-dollarize input is reported paste-back.

---

## Coverage summary

| Leak | Attio disposition |
|---|---|
| 1 Slow lead response | Measured (median time-to-first-touch) |
| 2 Anonymous visitors | Partial (identified-but-unworked slice; total traffic is paste-back) |
| 3 No lead scoring | Measured (structural probe + band coverage) |
| 4 Wrong-customer / ICP | Measured (win rate by segment, misdirected pipeline) |
| 5 Tech-stack sprawl | Paste-back (not CRM-measurable) |
| 6 Content / SEO / organic | Partial (organic share only if source is tracked) |
| 7 Outbound / sequencing | Partial (engagement tool is primary; CRM cross-check only) |
| 8 Nurture / dormant | Measured (dormant count and share) |
| 9 Conversion-content | Paste-back (not CRM-measurable) |
| 10 MQL to SQL / handoff | Measured (stage conversion, stalls, unexplained exits) |
| 11 RevOps / hygiene | Measured (dedup, ownership, staleness, competing fields) |

When Attio is connected, the connect-and-measure offer names it for leaks 1, 3, 4, 8, 10, 11 (full) and 2, 6, 7 (partial, with the paste-back boundary stated). Leaks 5 and 9 are always paste-back regardless of connection. Every measured figure states its query and record count; every reported figure says the buyer supplied it; every benchmark figure names the substitution. That is the whole provenance promise, kept per row.
