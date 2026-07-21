---
routine: gtm-engineer/roi-rollup
schedule: First Tuesday of each calendar quarter, 9am local
owner: Founder / Head of GTM
---

# Routine: Quarterly ROI Roll-Up (predicted vs measured, with the math)

## Trigger
First Tuesday of each calendar quarter, 9am local (the day after the portfolio re-audit, so it reads that quarter's freshly graded predictions). Schedule via **`/schedule`** (Claude Code cloud routine; it keeps firing after the session ends). If `/schedule` isn't available, fall back to the OS scheduler (launchd on macOS, cron elsewhere) invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

This is the ROI one-pager (plan §9, §10). It reads the structured ROI ledger, `roi-ledger.json`, and turns its machine-readable rows into a single buyer-private page: for every system, what recovery was predicted, what was measured, and the exact calculation chain that connects the two. It is the proof layer of the engagement, the artifact that answers "is this engine actually paying for itself," and its rows are what the Next-System Forecaster (roadmap) will eventually model against.

Two properties are non-negotiable. It is **buyer-private**: it reports only this buyer's own numbers, never a cross-buyer aggregate (that is the counsel-gated Outcome Beacon, which stays on the roadmap until cleared). And it is **substantiation-safe**: every figure carries its provenance and its calculation, no figure is asserted without the chain that produced it, and no invented sample size or confidence interval ever appears.

## Preconditions

Verify each at install time:

- **A structured ROI ledger with rows.** `~/.claude/artemis/gtm-engineer/ledgers/roi-ledger.json` exists and holds at least one row. Each row carries: system, metric, baseline, predicted, measured, provenance, dollarized delta, and the calculation. Rows are seeded at each system's verification and updated by the portfolio re-audit and portfolio review. No rows, no roll-up; ship at least one system and let its measurement land first.
- **The health-score and portfolio ledgers readable** for cross-reference (`ledgers/health-score-ledger.md`, `ledgers/portfolio-ledger.md`), so a dollar delta can be tied back to the prediction that made it.
- **A delivery channel (optional).** The buyer's chosen channel, defaulting to the engineer-namespace `#gtm-engine`; otherwise the one-pager lands in the artifacts dir as the deliverable.

**Degraded mode:** if a row is missing its `measured` value (the system shipped but has not accumulated a full quarter of outcome data), report that row as "predicted $X/yr, measurement pending, first full read expected [date]" rather than treating a blank as zero recovery. A prediction not yet measurable is honest and stays on the page as pending; it is never graded as a miss and never dollarized as if measured. If the ledger is unreadable, produce no page and leave a heartbeat noting the read failure.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are producing the quarterly ROI one-pager for [Company Name] from the structured ROI
ledger. This is a proof artifact, buyer-private and substantiation-safe. You write the page
and append a rollup snapshot; you change nothing in any live system.

STEP 1, read the structured ledger. Load
~/.claude/artemis/gtm-engineer/ledgers/roi-ledger.json. Each row carries system, metric,
baseline, predicted, measured, provenance, dollarized delta, and calculation. Cross-reference
health-score-ledger.md and portfolio-ledger.md so each dollar delta ties back to the
prediction that made it.

STEP 2, build the per-system lines. For each system with a row this quarter, produce one
block:
 - The metric, its baseline, and its measured value this quarter, each with its provenance
 label (measured from a named connected source with the record count, reported from buyer
 paste-back, or benchmark, never a bare number).
 - PREDICTED recovery: the $/yr the build projected, with the assumption line it rested on.
 - MEASURED recovery: the $/yr the ledger's measured delta supports, shown as the full
 calculation chain (the arithmetic from baseline and measured metric to dollars, exactly
 as the row's calculation field records it). Never assert a recovery figure without the
 chain beneath it.
 - PREDICTED vs MEASURED: the gap, and one honest line on why (the call held, beat, or
 missed). A row whose measured value is still pending is labeled "measurement pending,"
 not counted as zero, not graded as a miss.

STEP 3, total honestly. Sum measured recovery across systems with the anti-double-count rule
applied (overlapping leaks are netted, never stacked), and state the pending rows separately
so the total is "measured to date," not an inflated blend of measured and hoped-for. If the
engine is paying for itself on measured numbers alone, say so plainly with the arithmetic; if
it is not yet, say that just as plainly and name what has to land.

STEP 4, write and post the one-pager. Save it to
~/.claude/artemis/gtm-engineer/diagnostics/artifacts/roi-rollup-[YYYY-Qn].md. Post the
summary via slack.digest to the buyer's chosen channel (engineer-namespace default
#gtm-engine), system-tagged, leading with measured-recovery-to-date and the predicted-vs-
measured headline. Idempotency: one roll-up per quarter; the key is
gtm-engineer.roi-rollup.<YYYY-Qn>. If this quarter's is already posted, UPDATE that thread
rather than post a second. Buyer-private: this page reports only this buyer's own figures;
never include a benchmark as if it were a measured result and never compare the buyer to
other buyers.

STEP 5, append the rollup snapshot. Append one dated snapshot row to
~/.claude/artemis/gtm-engineer/ledgers/roi-ledger.json under a rollups array (the quarter,
measured-recovery-to-date, pending count, and the systems that beat or missed), so the
Forecaster has a quarter-over-quarter series to model against later. This is a memory write
to the ledger, the ONLY write this routine makes; it changes nothing in any live system.

STEP 6, touch the registry. Update this routine's row in
~/.claude/artemis/gtm-engineer/routines-registry.json (name: gtm-engineer.roi-rollup),
setting last_confirmed_fire to today. It writes a page and appends a snapshot on every run,
so those are the dated trace; no separate heartbeat is needed. Local state write, never a
live-system change.

Action policy: read, compute, report, and append the snapshot. No external mutation, ever.
Every figure carries its provenance and its calculation chain. No cross-buyer aggregate, no
benchmark dressed as a measured result, no invented sample size, no confidence interval.

Use only the ledger's real rows. A prediction not yet measurable stays pending, never zero,
never a miss.
```

## Why the calculation chain is mandatory

The roll-up's credibility is the chain, not the headline. "Recovered about $310K/yr on the response leak" means nothing on its own; "median response dropped from 41 hours to under 5 minutes, which at [monthly leads] x [lead-to-opp lift] x [deal size] nets about $310K/yr, measured from HubSpot across [the quarter's records]" is a number the buyer can defend to their board and a number the Forecaster can model. Every measured recovery on the page shows its arithmetic. A figure without its chain does not go on the page.

## Namespacing

Registry name `gtm-engineer.roi-rollup`; idempotency key `gtm-engineer.roi-rollup.<YYYY-Qn>`; digest channel default the engineer-namespace `#gtm-engine`, system-tagged. Engineer namespace throughout.

## Going live

Not live until it has fired once unattended. The first roll-up may be mostly pending rows if systems shipped recently; the pass bar is a correctly shaped page that shows predicted, shows what is measured, and labels the rest pending. Confirm the first unattended page with the buyer before calling the proof layer done.

## Anti-hallucination

Every measured recovery traces to a `roi-ledger.json` row with its metric, its provenance, and its calculation. Pending stays pending. No benchmark presented as measured, no cross-buyer number, no invented sample size, no confidence interval. The page is only as strong as the chains on it.
