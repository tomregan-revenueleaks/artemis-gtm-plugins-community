# Amplemarket Diagnostic Queries, the read-only leak-measurement library for the outbound surface

This is the per-leak query library the diagnostic engine's outbound analyst runs against a connected Amplemarket account during connect-and-measure. Every entry names one outbound-motion leak, the exact read that measures it, the window, the computation, and the provenance label the number carries into the diagnosis. It is the first live read of the buyer's real outbound numbers, the baseline everything later diffs against. The four ongoing Amplemarket routines (`modules/amplemarket/routines/`) run these same reads on a schedule after the build; this file is the one-time diagnostic version they inherit from.

Phase-loaded during DIAGNOSE, and again at the Phase 5 analytics baseline of an Amplemarket build. It is read-only end to end. Nothing here sends, drafts, enrolls, pauses, enriches, reveals, or edits anything in the account, and nothing here consumes a credit (see the read-surface note below). It reads, it computes, it labels provenance, and it hands the numbers to the leak-quantifier. Consistent with the §7.3 capability-split in `modules/amplemarket/`: the Amplemarket MCP writes are sequence, lead-list, and enrollment operations; everything this file touches is on the read side of that split.

Canon this file obeys: leak IDs and the no-leak-no-recommendation rule come from `diagnose/leak-taxonomy.md`; every benchmark band is single-sourced from `diagnose/benchmarks.md` and is never itself counted as a leak (the benchmark-fill circularity guard); the provenance labels and the record-count discipline are from `diagnose/playbook.md` and `output-integrity.md`; the baseline this seeds becomes the outbound results ledger per `results-memory.md`.

---

## The read surface (what the MCP gives us, honestly)

Only these reads are used, and only these. All are free (no credit debit) and read-only. Reveal and search calls that debit the admin's credit balance (`enrich_person`, `enrich_company`, and any reveal path) are deliberately excluded: a diagnostic that quietly spends the buyer's credits is not an honest diagnostic.

| Read | Returns | Used by leaks |
|---|---|---|
| `list_sequences` | every sequence and its state (`active` / `draft` / `paused`) | L4, L2, L3, L6 |
| `get_sequence` | one sequence's step structure: step count, channel per step (email / linkedin_* / phone_call), step order | L3, L7 |
| `ask_analytics` then `get_analytics_result` | natural-language analytics answer (async, roughly 30s); reply rate, open rate, per-step breakdown, per-mailbox health, credit burn | L1, L2, L3, L6, L7 |
| `list_sequence_leads` / `get_sequence_lead` | leads enrolled in a sequence and their progress | L6 |
| `list_inbox_threads` / `get_inbox_thread` | Unibox threads (email and LinkedIn), inbound message bodies and timestamps | L5 |
| `list_outbox_entries` / `get_outbox_entry` | sent messages, for reply-to-sequence context | L5 |
| `list_lead_lists` / `get_lead_list` | lead lists, their size, and their type (`linkedin` / `email` / `titles_and_company`) | L6, L7 |
| `get_messaging_settings` | the value props, tone, and per-persona messaging authored in the console | L8 |
| `list_accounts` / `get_account`, `list_company_job_openings` | signal presence (accounts, hiring signals), a health read only | context |

**The one hard limit that shapes every entry below: `ask_analytics` is natural-language bounded with no guaranteed schema.** It answers a concrete question well, and it does not promise a stable per-mailbox or per-sequence table. When it returns the number, that number is `measured`. When it cannot break the answer out the way a leak needs (most commonly per-mailbox deliverability, and credit burn on some accounts), the read degrades to a named buyer export, and the number it produces is `reported` (paste-back), not `measured`. Neither is worth less; they just carry different provenance, and the diagnosis says which.

**Reads that live only in the console, never in the MCP:** the live Email Warmup column state, the remaining credit balance on some accounts, inbox-placement / seed-test scores, and DNS-auth pass state. These are always `reported` (buyer pastes back the console value or the export). This file never asserts a console-only value it did not see; it asks for it and labels it.

---

## Provenance labels (attached to every figure)

- **measured** = an MCP read returned it. The entry records the query run and the record count it covered (sequences scored, threads read, mailboxes returned). No count, no `measured` label.
- **reported** = the buyer pasted it back (a console value, a named export). Honest and usable; just not machine-read.
- **benchmark** = a labeled substitution from `diagnose/benchmarks.md`, used only as the comparison line a measured number is judged against. A benchmark is never itself booked as a leak. That is the circularity guard: the leak is the buyer's own measured gap below the band, never the band.

If a leak's read fails and no export lands, that leak produces no number, and no number means no recommendation on it (`diagnose/leak-taxonomy.md`). The diagnosis says "could not measure on this account this session," it does not fill the hole with a benchmark and call it a finding.

---

## Scope: what this surface can and cannot see

Amplemarket is the outbound execution surface. It measures outbound-motion leaks and only those. It has no view of CRM routing, lead-to-account matching, ICP scoring hygiene, anonymous-visitor identification, MQL-to-SQL handoff, or nurture stage. Those leaks are measured by the CRM and marketing-automation adapters, not here. Depth over breadth (§6): this library ships complete for every outbound leak the surface can honestly read, and stays silent on the ones it cannot. When the connect-and-measure offer runs on Amplemarket alone, it names only the leaks below.

**Anti-double-count, stated once and enforced across the set.** Deliverability (L1) sits upstream of reply rate (L2) and cadence (L3). When L1 is failing, a reply-rate gap is attributed to L1, not booked a second time as a copy or cadence leak. The rule the outbound routines already run applies here: warmth before copy. Measure L1 first; if it is red, the L2 and L3 gaps are annotated "downstream of L1, not separately quantified" until deliverability clears. One root cause, counted once.

---

## The leak library

Each entry: the leak, the read, the window, the computation, the provenance, the benchmark it is judged against, the degraded (paste-back) fallback, and the guard.

### L1. Deliverability decay (domain and mailbox reputation)

The upstream leak. Burned or unwarmed sending means the mail never reaches a human, and no sequence cleverness recovers a red sender. Measure this first; it gates the reading of L2 and L3.

- **Reads:** `ask_analytics` (then `get_analytics_result`), per mailbox on the roster: bounce rate, spam-complaint rate, open rate this window vs prior window, reply rate. Warmup runway and inbox placement are console-only.
- **Window:** last 7 days for the operational health read; last 30 days for the diagnostic baseline trend.
- **Computation:** flag any mailbox where spam-complaint rate is over 0.3% (pause candidate, the top-of-report scream), over 0.1% and up to 0.3% (early-warning watch), bounce rate over 2% (list-hygiene or under-warmed), open rate down more than 5 points week over week (reputation slipping), or inbox placement under 70% when measured. Warmup runway under roughly 4 weeks on a mailbox already sending real outbound is a hold.
- **Provenance:** `measured` when `ask_analytics` returns a genuine per-mailbox breakout (record count = mailboxes returned). Per-mailbox schema is often unavailable, in which case: `reported`. Warmup column state, inbox-placement scores, and DNS-auth state are always `reported` (console / seed test).
- **Benchmark source:** the deliverability thresholds above are locked operating thresholds from `diagnose/benchmarks.md`, not soft guidance; do not soften them.
- **Degraded fallback:** request the named export, per-mailbox bounce, spam, open, and reply for the last 7 days, plus the Email Warmup column state per mailbox (Account Settings > Email Warmup) and the latest seed/placement score. Proceed once it lands; label every value `reported`.
- **Guard:** never reshape an account-level aggregate into a fake per-mailbox number. If it cannot break out by mailbox and no export lands, L1 reads "unconfirmed this session" and the downstream leaks stay unquantified rather than attributed to copy.

### L2. Sequence reply-rate underperformance

The headline outbound outcome leak: live sequences replying below the band their job should hit.

- **Reads:** `list_sequences` to enumerate `active` sequences, then `ask_analytics` per active sequence: open rate, reply rate, active (enrolled and progressing) vs completed leads.
- **Window:** last 90 days for the diagnostic baseline (a stable read across the sending history); last 7 days for the weekly operational diff the routine later runs.
- **Computation:** for each active sequence, reply rate against (a) the band for its intended job from `diagnose/benchmarks.md` and (b), post-build, its own prior baseline. The leak is the measured gap below the band, per sequence, summed across active sequences, never the band itself.
- **Provenance:** reply and open rates are `measured` (record count = active sequences scored). The comparison band is `benchmark`.
- **Benchmark source:** `diagnose/benchmarks.md` outbound bands (static-list cadences and signal-triggered cadences run different bands by sequence type). Labeled benchmark, never an invented sample size, never counted as a leak.
- **Degraded fallback:** if `ask_analytics` will not return per-sequence reply rates, request the per-sequence export (opens, replies, active/completed counts, last 7 days plus 90-day baseline); label `reported`.
- **Guard:** if L1 is red, annotate the gap "downstream of deliverability, not separately quantified" and do not book it as a copy leak (anti-double-count). No sequence data at all means no L2 finding.

### L3. Cadence depth and follow-up fatigue

Reply rate can sit in-band on the first touch and still leak, because the cadence is too shallow or a later touch has gone dead. This leak reads structure, not just the outcome.

- **Reads:** `get_sequence` per active sequence for step count, channel per step, and order; `ask_analytics` for the per-step opens/replies breakdown.
- **Window:** current structure (point-in-time) for the shape; last 30 days for per-step reply decay.
- **Computation:** flag sequences with too few touches for their job, sequences intended as multichannel whose `linkedin_*` or `phone_call` steps are producing nothing relative to their email steps, and fatiguing steps (a strong touch-1 reply with a near-zero touch-3-and-later reply). The finding is the specific step number and channel that is dead, and the rebuild it implies.
- **Provenance:** step structure is `measured` from `get_sequence` (record count = sequences inspected). Per-step reply decay is `measured` when `ask_analytics` breaks it out, otherwise `reported` via export.
- **Benchmark source:** cadence-depth and channel-mix guidance from `diagnose/benchmarks.md`.
- **Degraded fallback:** request the per-step export (opens and replies by step number, last 30 days) alongside the structure read; label `reported`.
- **Guard:** structural constraint is honest in the finding: once a sequence is live, cadence is frozen and steps append only to the end, so the fix is a rebuilt draft, not a mid-insert. Do not synthesize per-step numbers the data did not return. Downstream of L1 when L1 is red.

### L4. Unlaunched pipeline (drafts built but never activated)

The highest-leverage read on the surface, and the cheapest to measure. A sequence sitting in `draft` after build is sending exactly zero; the pipeline is parked at the human activation switch.

- **Reads:** `list_sequences`, reconciled against the build's Phase 4 sequence inventory when one exists.
- **Window:** point-in-time.
- **Computation:** count sequences in `draft` state that were intended to be live (no activation record). Each one is a fully-built cadence producing nothing. Also note `paused` sequences and ask whether the pause was an intentional deliverability hold or an orphan.
- **Provenance:** `measured` (record count = sequences in each state). This is the cleanest measured number in the library: state is a first-class field.
- **Benchmark source:** none; a draft that was supposed to be live is a binary finding, not a band comparison.
- **Degraded fallback:** none needed; `list_sequences` is the smallest, most reliable read (it doubles as the MCP-reachability check at install).
- **Guard:** the finding is a recommendation to activate, surfaced loudly. Activation is a one-time human action in the dashboard; this read never activates anything.

### L5. Reply-triage and speed-to-respond

A positive reply that sits unanswered cools fast; a hot reply left over a weekend is a dead hot reply. This leak reads the Unibox for interested replies and their age.

- **Reads:** `list_inbox_threads` over the window (email and LinkedIn both, the Unibox spans both), then `get_inbox_thread` on the threads to classify; `list_outbox_entries` for which sequence and step each reply answers.
- **Window:** last 14 days for the diagnostic (long enough to catch a triage backlog); the routine later runs a daily weekday sweep.
- **Computation:** classify inbound replies (positive/interested, question, objection, referral, not-now, negative/DNC, auto/OOO), then measure how many positive/interested replies are aged past a same-day response and still open. The leak is the count and age of hot replies not yet actioned.
- **Provenance:** `measured` (record count = threads read). Page the reads; a busy Unibox can exceed the MCP output-token cap, so pull headers first, then read only the threads being classified, and summarize per thread.
- **Benchmark source:** the speed-to-respond expectation from `diagnose/benchmarks.md`; a sub-5-minute need on hot inbound is the speed-to-lead motion, flagged as a handoff, not solved here.
- **Degraded fallback:** if the Unibox read fails, say so and ask the human to eyeball it this session (positives first); never reconstruct thread contents from memory.
- **Guard:** the MCP is read-only on the inbox; it cannot send, draft, route, or mark handled. Every reply and action stays manual in the Unibox. The finding prioritizes; it never answers anyone.

### L6. Enrollment volume and coverage

Lists get built and then leads never get enrolled, or active-lead counts sit far below the sending capacity the infrastructure can carry. Effort spent building, nothing spent sending.

- **Reads:** `list_lead_lists` / `get_lead_list` for list sizes and types; `list_sequences` and `list_sequence_leads` for active vs completed enrolled counts per sequence.
- **Window:** point-in-time for counts; last 30 days for enrollment trend.
- **Computation:** compare total leads built into lists against leads actually enrolled and progressing, and compare active enrolled volume against the per-mailbox daily-send capacity (roughly 150-200 per mailbox per day and roughly 50 new leads per mailbox per day are the locked ceilings, never the floor). The leak is built-but-unenrolled inventory and chronic under-enrollment against capacity.
- **Provenance:** `measured` (record count = lists and sequences read).
- **Benchmark source:** the send-volume ceilings and enrollment guidance from `diagnose/benchmarks.md`; these are safety caps, so "under capacity" is judged against the runway a warmed sender can safely carry, not against the raw ceiling.
- **Degraded fallback:** request the list-and-enrollment export (list sizes, enrolled/active/completed per sequence); label `reported`.
- **Guard:** do not recommend scaling enrollment past what L1 says the sender can safely carry. Coverage is judged against warm capacity, never against the raw daily cap; deliverability gates volume.

### L7. Enrichment and credit waste

The quiet money leak specific to all-in-one platforms: reveal spend that does not earn replies. Credits are debited from the account admin, not the rep who runs the enrichment.

- **Reads:** `ask_analytics` for credit burn broken out by enrichment vs email-reveal vs phone-reveal, by lead list, and by user where exposed; `list_lead_lists` for list types (`linkedin` and `titles_and_company` lists always enrich; only `email` lists can disable it); `get_sequence` to check whether phone-reveal spend maps to any `phone_call` step.
- **Window:** current month for burn; cross-referenced against L2 for whether the spend earns replies.
- **Computation:** flag phones revealed on personas or sequences with no phone-call stage (paying to reveal phones nobody dials), email reveals far exceeding leads actually enrolled, always-enriched list types built larger than the ICP needs, and same-day re-reveals that should have hit the free 24h enrichment cache. The leak is reveal spend not tracking to enrolled, replying pipeline.
- **Provenance:** credit burn is `measured` when `ask_analytics` returns it; on accounts where it will not, `reported` via export. Remaining credit balance is `reported` (console/billing view on many accounts). List types are `measured` from `list_lead_lists`.
- **Benchmark source:** the enrich-on-enroll-not-on-build discipline and cache-window guidance from `diagnose/benchmarks.md`; no dollar figures are asserted here (credit pricing changes and is not this file's to quote).
- **Degraded fallback:** request the credit export (consumed this month by enrichment / email-reveal / phone-reveal, by lead list, plus remaining balance); label `reported`. Never estimate credit spend; on a paid credit system a fabricated number is worse than an honest "could not read it."
- **Guard:** read-only and cost-free by construction, this entry never runs a reveal to measure reveal waste. The finding recommends guardrails to the admin; it never changes a plan, a seat, or a spend limit.

### L8. Messaging and personalization gap

AI copy reads generic when the messaging settings were left blank, and prospects clock a bot in the first line. This leak reads whether the positioning the AI inherits from actually exists.

- **Reads:** `get_messaging_settings` (read-only; it cannot write the settings, which is why they must be authored by hand in the console).
- **Window:** point-in-time.
- **Computation:** the finding is binary-plus: are the value props, tone, and per-persona messaging populated, or empty? Empty or thin settings mean every generated sequence inherits generic copy, so this leak is upstream of an L2 reply-rate gap on any AI-drafted cadence.
- **Provenance:** `measured` (present or absent, from one read).
- **Benchmark source:** none; populated-or-not is not a band.
- **Degraded fallback:** none needed; this is a single reliable read.
- **Guard:** when settings are empty, the finding is "author the messaging before judging the copy," and any L2 reply gap on AI-drafted sequences is annotated as downstream of L8, not double-counted as a separate copy leak (anti-double-count).

---

## Running the sweep (the connect-and-measure order)

Run the reads in dependency order so the anti-double-count rule holds:

1. **Reachability + L4** (`list_sequences`). Smallest read, confirms the MCP path is alive, and simultaneously returns the unlaunched-draft count. If this fails, stop and fix the connection (or fall to labeled paste-back for the whole surface) before promising any measured number.
2. **L1 deliverability.** Measure first, because it gates L2 and L3. If red, annotate the downstream leaks and do not separately quantify them.
3. **L2, L3.** Sequence outcome and cadence structure, quantified only where L1 is clean.
4. **L8.** Messaging settings; if empty, annotate AI-drafted L2 gaps as downstream.
5. **L5, L6, L7.** Reply triage, coverage, credit waste, independent of the L1 chain.

Then hand the labeled figures to the leak-quantifier. Each figure carries its provenance label and, for every `measured` row, the query run and the record count it covered. The output-integrity pass (`output-integrity.md`) records the query text for the measured rows, so "every number carries its provenance" is literally true on the page.

At an Amplemarket build's Phase 5, this same sweep is the baseline seed for `results-ledger.md` (`results-memory.md`): if the account is brand-new with no sends, the honest baseline is "zero, and that is the starting line," recorded anyway so the 30/60/90-day review has something to diff against.

## The boundary, restated

Read-only, cost-free, provenance-labeled. This library measures the outbound leaks the Amplemarket surface can honestly see, attributes each to one root cause, judges each measured number against a labeled benchmark it never counts as a leak, and produces no number it could not read. Where a value lives only in the console or would cost a credit to reveal, it asks and labels `reported` rather than inventing `measured`. The writes that fix these leaks (activation, rebuilt drafts, enrollment, guardrails) are all human dashboard actions or the module's own write-lane work; this file only tells the buyer, with receipts, where the outbound motion is leaking.
