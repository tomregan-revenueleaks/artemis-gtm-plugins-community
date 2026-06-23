---
routine: roi-recovery-diagnostic/quarterly-rerun
schedule: First Monday of each calendar quarter, 9am local
owner: Founder / Head of GTM / RevOps lead
---

# Routine: Quarterly Recoverable-Revenue Re-run (Artemis Ledger)

## Trigger
First Monday of each calendar quarter. Schedule via **`/schedule`** (Claude Code cloud routines — they keep firing after the session ends) after the initial ROI diagnostic completes and the buyer wants to track recoverable revenue shrinking over time. If `/schedule` isn't available in the buyer's environment, fall back to the OS scheduler: launchd (macOS) or cron, invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

**Going live:** the routine is not live until it has fired once unattended. Agree on the check with the buyer at install time — the next quarter's re-run report appearing on its own (in `~/.claude/artemis/roi-recovery-diagnostic/artifacts/` or the agreed Slack channel) — and only then call the trendline layer done.

The point of the re-run: a one-time diagnostic tells you what your funnel leaks today. The quarterly re-run tells you whether the lever you fixed actually moved, whether the recoverable figure is shrinking, and whether a new gap opened as the company grew. Recoverable revenue is supposed to go DOWN as you close the gap — this routine confirms it is.

## Preconditions

Required before scheduling — verify each at install time:

- **The initial diagnostic is complete and on disk.** `~/.claude/artemis/roi-recovery-diagnostic/` contains `discovery.json` (the baseline seven inputs) and `recoverable-ledger.md` with the baseline row. Verify: `ls ~/.claude/artemis/roi-recovery-diagnostic/recoverable-ledger.md` returns the file and it contains the Baseline row. No baseline, no trendline — run the diagnostic first.
- **A path to fresh quarterly numbers.** Either a CRM connected to Claude or the buyer's commitment to paste this quarter's seven funnel numbers when the routine asks. Note which in `discovery.json`'s `connections` map.
- **A delivery channel (optional).** Slack or email connected if the buyer wants the re-run report pushed; otherwise it lands in the artifacts dir.

**Degraded mode:** if no fresh numbers are available when the routine fires (no connected tool, no buyer response), do not fabricate a reading — carry forward last quarter's values labeled "carried forward, not re-measured," write the report to `~/.claude/artemis/roi-recovery-diagnostic/artifacts/` anyway, and flag which metrics need real numbers next session. If Slack/email isn't connected, skip the push and surface the report at the next session open.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the quarterly recoverable-revenue re-run for [Company Name], comparing this quarter to the last recorded diagnostic. Read the prior state from ~/.claude/artemis/roi-recovery-diagnostic/ (discovery.json for the baseline inputs, recoverable-ledger.md for the trendline so far).

Pull the same seven funnel inputs as the original ROI diagnostic (use the discovery-questions template; pull directly from the connected CRM if one is wired, otherwise ask for paste-back):
1. Current ARR
2. Average deal size
3. Monthly leads
4. Lead-to-opp rate
5. Win rate
6. Sales cycle (days)
7. Number of AEs

Then re-run the recoverable-revenue methodology (playbook Steps 2-7):
- Re-benchmark lead-to-opp, win rate, and cycle against the median and top-quartile bars
- Recompute currentAnnual and optimizedAnnual, and totalRecoverable = max(0, optimized − current)
- Re-split the total proportionally across the lead-to-opp and win-rate levers (verify they reconcile)
- Recompute the cycle-velocity figure, kept SEPARATE from the recoverable total
- Recompute cost-of-delay (÷12, ÷52, ÷365)
- Diff against last quarter

Output a TREND report, not a fresh diagnostic:

1. RECOVERABLE REVENUE TREND
 - This quarter's recoverable figure vs last quarter vs the original baseline
 - Show the trajectory: e.g., "Baseline $4.4M → Q1 $3.1M → Q2 $2.0M. The leak is closing."
 - Recoverable revenue should be SHRINKING as the buyer fixes the lever. If it GREW, that's a regression — name it loudly (usually a lever slipped, or lead volume grew faster than conversion).

2. LEVER MOVEMENT
 - Lead-to-opp: last quarter's rate vs this quarter's. Did the qualification-automation work move it? Quantify the recovered slice: "lead-to-opp moved 15% → 21%, recovering ~$X of the gap."
 - Win rate: same.
 - Cycle: days-faster movement (velocity, still reported separately).

3. GAPS STILL OPEN
 - Which levers still trail benchmark? Re-quantify at current numbers (the $ figure moves as volume/deal size change).
 - Flag any lever that got WORSE quarter-over-quarter.

4. NEW GAPS
 - A lever that was at/above benchmark last quarter and crossed below this quarter.
 - Usually growth-induced: more lead volume exposed a conversion gap, a new segment dragged win rate.
 - For each new gap, map to the matching Artemis agent (lead-to-opp → Qualification Automation; win rate → Conversion Content).

5. RECOMMENDED NEXT MOVE
 - If the recoverable figure is shrinking and the lever is moving, say so plainly: "The leak is closing. Next quarter, watch [the remaining lever]."
 - If a gap is still open and material, surface the matching agent (playbook Step 7; no gap, no recommendation).
 - If two levers are open, surface the GTM System bundle math (coverage framing at two gaps, savings only at 3+).

Respect the model's rules: ONE optimized funnel minus current (never sum the gaps), cycle is velocity not recoverable ARR, and never quantify against a benchmark-filled field. Only quantify with the buyer's current numbers or named benchmark substitutions. If fresh numbers aren't available, carry forward last quarter's values labeled "carried forward, not re-measured" rather than fabricating a reading.

Write the outputs to disk: append this quarter's row to ~/.claude/artemis/roi-recovery-diagnostic/recoverable-ledger.md and save the full trend report to ~/.claude/artemis/roi-recovery-diagnostic/artifacts/rerun-[YYYY-Qn].md. Then post the trend summary to the buyer's preferred channel (Slack #revenue-ops or email) if one is connected; if not, the artifacts copy is the deliverable. Lead with the recoverable trajectory and the headline: "[Company] recoverable revenue: [figure] ([+/-X] vs last quarter)."
```

## The ledger (the durable trendline)

The trend lives in one named file: **`~/.claude/artemis/roi-recovery-diagnostic/recoverable-ledger.md`**. The initial diagnostic writes the Baseline row at close; this routine appends one row per quarter. The ledger is the source of truth — the trend is read from it, never re-derived from memory:

| Quarter | Recoverable (top-quartile) | Recoverable (median) | Biggest Lever | Lever $ | Cost of Delay (weekly) |
|---------|----------------------------|----------------------|---------------|---------|------------------------|
| Baseline | | | | | |
| Q1 | | | | | |
| Q2 | | | | | |

A healthy trajectory: the recoverable figure shrinking, the biggest lever's rate climbing toward benchmark, the weekly cost-of-delay falling. When recoverable revenue reaches ~$0, the funnel conversion is at top quartile — the leak is closed.

## Anti-hallucination

Re-benchmark only metrics the buyer re-provides this quarter. If they don't have fresh numbers for a field, carry forward last quarter's value and label it "carried forward, not re-measured"; don't fabricate a new reading. Never sum the gaps (one optimized funnel minus current), never add cycle to the total. The recoverable trend is only credible if every point on it traces to real inputs.

## What success looks like

The buyer has a 90-day cadence that shows recoverable revenue as a line going DOWN and to the right (the leak closing), catches a new gap the quarter it opens, and re-surfaces the matching agent or the GTM System bundle at exactly the moment the data justifies it.
