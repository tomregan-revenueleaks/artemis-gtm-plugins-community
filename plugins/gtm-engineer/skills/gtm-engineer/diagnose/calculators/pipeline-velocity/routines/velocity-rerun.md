---
routine: pipeline-velocity/velocity-rerun
schedule: First Monday of each calendar quarter, 9am local
owner: VP of Sales / RevOps lead / Founder
---

# Routine: Quarterly Pipeline Velocity Re-Run

## Trigger
First Monday of each calendar quarter. Schedule via **`/schedule`** (Claude Code cloud routines, they keep firing after the session ends) after the initial velocity diagnostic completes and the buyer wants to track whether the lever they pulled actually moved the number. If `/schedule` isn't available in the buyer's environment, fall back to the OS scheduler: launchd (macOS) or cron, invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

**Going live:** the routine is not live until it has fired once unattended. Agree on the check with the buyer at install time, the next quarter's trend report appearing on its own (in `~/.claude/artemis/gtm-engineer/diagnostics/pipeline-velocity/artifacts/` or the agreed Slack channel), and only then call the trendline layer done.

The point of the re-run: a one-time read tells you how fast your pipeline moves today and which lever to pull. The quarterly re-run tells you whether the lever you pulled actually lifted velocity, whether the dollar gap to benchmark is shrinking, and whether a different lever has become the new constraint as the others improved. Velocity drifts, and pulling one lever often exposes the next. This catches both every 90 days.

## Preconditions

Required before scheduling, verify each at install time:

- **The initial diagnostic is complete and on disk.** `~/.claude/artemis/gtm-engineer/diagnostics/pipeline-velocity/` contains `discovery.json` (the baseline inputs) and `velocity-ledger.md` with the baseline row. Verify: `ls ~/.claude/artemis/gtm-engineer/diagnostics/pipeline-velocity/velocity-ledger.md` returns the file and it contains the Baseline row. No baseline, no trendline, run the diagnostic first.
- **A path to fresh quarterly numbers.** Either a CRM connected to Claude (see `tool-connections.md`) or the buyer's commitment to paste this quarter's four inputs when the routine asks. Note which in `discovery.json`'s `connections` map.
- **A delivery channel (optional).** Slack or email connected if the buyer wants the trend report pushed; otherwise it lands in the artifacts dir.

**Degraded mode:** if no fresh numbers are available when the routine fires (no connected tool, no buyer response), do not fabricate a reading, carry forward last quarter's values labeled "carried forward, not re-measured," write the report to `~/.claude/artemis/gtm-engineer/diagnostics/pipeline-velocity/artifacts/` anyway, and flag which inputs need real numbers next session. If Slack/email isn't connected, skip the push and surface the report at the next session open.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the quarterly pipeline velocity re-run for [Company Name], comparing this quarter to the last recorded read. Read the prior state from ~/.claude/artemis/gtm-engineer/diagnostics/pipeline-velocity/ (discovery.json for the baseline inputs, velocity-ledger.md for the trendline so far).

Pull the same inputs as the original diagnostic (use the discovery-questions template; pull directly from the connected CRM per tool-connections.md if one is wired, otherwise ask for paste-back):
1. Pipeline profile, industry, growth motion (re-confirm motion; companies shift PLG→Hybrid as they add sales)
2. The four inputs, monthly leads + lead-to-opp rate (convert to opportunities. NEVER run raw leads as opps), average deal size, win rate, average sales cycle in days
 (For PLG: monthly signups, trial-to-paid as win rate, ARR per account as deal size, time-to-convert as cycle)

Then re-run the methodology (playbook Phases 3-6):
- Convert leads to opportunities (opps = leads × lead-to-opp)
- Recompute velocity in $/day and annualize it
- Re-benchmark against the industry × motion row; recompute the ratio, grade A-F, and the annual dollar gap (the leak)
- Re-run the four-lever simulation; rank by $/day delta re-weighted by benchmark gap; name the current winning lever
- Diff against last quarter

Output a TREND report, not a fresh diagnostic:

1. VELOCITY TREND
 - This quarter's $/day velocity vs last quarter vs the original baseline
 - Show the trajectory: e.g., "Baseline $3,667/day → Q1 $4,210/day → Q2 $4,980/day. +36% in two quarters."
 - Grade movement (D → C → B). Flag if velocity DROPPED; that's a regression, name it loudly.

2. THE LEVER YOU PULLED (the prediction calibration)
 - This is where you reconcile last cycle's prediction against reality. Three reads, in plain language:
 b. Did the metric actually move? "You pulled win rate; it went 18% → 23%, now at benchmark. Velocity rose $4,210 → $4,980/day. That's ~$280K/year of throughput recovered." If the pulled lever didn't move, say so plainly and ask what got in the way.
 c. Projected versus actual: compare the Projected impact you logged last cycle to what the lever actually recovered, and say whether the call held, overshot, or fell short.

3. THE DOLLAR GAP
 - Re-quantify the velocity leak at current numbers. Is the gap to benchmark shrinking? "Leak was $720K/year, now $310K/year, you've closed 57% of it."
 - If the gap grew, flag it.

4. THE NEW WINNING LEVER
 - Pulling one lever often makes a different one the new constraint. Re-run the simulation and name this quarter's winning lever.
 - "Win rate is now at benchmark, so your biggest remaining lever is cycle length, it's 30% over benchmark and worth ~$190K/year. That's the next pull."
 - Map the new winning lever to the matching Artemis agent + partner (playbook Phase 6).

5. RECOMMENDED NEXT MOVE
 - Lead with the current winning lever, its annual dollar impact, and its agent link.
 - If velocity is climbing and the gap is closing, say so plainly: "Velocity is trending up and you've closed most of the leak. Next quarter, pull [current winning lever]."
 - Only surface a bundle if 2+ levers are genuinely below benchmark this quarter (savings framing only at 3+).

Respect the basis: PLG re-runs frame velocity off trial-to-paid signups, SLG/Hybrid off opportunities. Never invent a metric the buyer didn't provide this quarter. Only quantify with the buyer's current numbers or named benchmark substitutions. Never run raw leads through the formula as opportunities. If fresh numbers aren't available, carry forward last quarter's values labeled "carried forward, not re-measured" rather than fabricating a reading.

Write the outputs to disk: append this quarter's row to ~/.claude/artemis/gtm-engineer/diagnostics/pipeline-velocity/velocity-ledger.md (the same six trend fields plus the two prediction fields: this quarter's Recommendation and Projected impact, so this row becomes the prior the NEXT re-run grades) and ALSO append one calibration line to the same ledger that reconciles last cycle's prediction: whether the buyer acted on the recommendation (bought or built the recommended agent, applied the fix), whether the metric actually moved, and the projected-versus-actual read, closed with one sentence of advisor judgment. Keep it human, one line, in the ledger's existing row voice. For example: "Projected a meaningful win-rate lift if they shipped the Conversion Content System; they built it and win rate climbed into the projected band, the cycle-leak call held." Or, when the buyer didn't act: "Recommended the Conversion Content System for the win-rate leak; they didn't ship it, win rate held flat, so the leak and the projection both stand for next cycle." This calibration line goes into the existing velocity-ledger.md (the only ledger this diagnostic keeps), never a separate results-loop file; the diagnostic ships no running system. Then save the full trend report to ~/.claude/artemis/gtm-engineer/diagnostics/pipeline-velocity/artifacts/velocity-rerun-[YYYY-Qn].md. Post the trend summary to the buyer's preferred channel (Slack #revenue-ops or email) if one is connected; if not, the artifacts copy is the deliverable. Lead with the velocity trajectory and the headline: "[Company] pipeline velocity: $[X]/day ([+/-X%] vs last quarter)."
```

## The ledger (the durable trendline)

The trend lives in one named file: **`~/.claude/artemis/gtm-engineer/diagnostics/pipeline-velocity/velocity-ledger.md`**. The initial diagnostic writes the Baseline row at close; this routine appends one row per quarter. The ledger is the source of truth, the trend is read from it, never re-derived from memory. Each row carries the six trend fields plus the two prediction fields (Recommendation, Projected impact) that the next re-run grades:

| Quarter | Velocity ($/day) | Grade | Benchmark ($/day) | Dollar gap ($/yr) | Winning lever | Recommendation | Projected impact |
|---------|------------------|-------|-------------------|-------------------|---------------|----------------|------------------|
| Baseline | | | | | | | |
| Q1 | | | | | | | |
| Q2 | | | | | | | |

Beneath each new quarter's row, append the one-line calibration that reconciles the PRIOR row's prediction: did the buyer act on the recommendation, did the metric move, and the projected-versus-actual read, in one sentence of advisor judgment. That one line is the whole prediction-calibration loop for this free diagnostic, the projection logged at one read, graded at the next, in this same ledger and no other file.

A healthy trajectory: velocity climbing, grade improving, dollar gap shrinking, the winning lever rotating as each constraint gets fixed, and the calibration line showing the predictions held.

## Anti-hallucination

Re-measure only inputs the buyer re-provides this quarter. If they don't have a fresh number for a field, carry forward last quarter's value and label it "carried forward, not re-measured"; don't fabricate a new reading. Always convert leads to opportunities before computing, never run raw leads as opps. The velocity trend is only credible if every point on it traces to real inputs.

## What success looks like

The buyer has a 90-day cadence that shows pipeline velocity as a line going up and to the right, confirms whether each lever they pulled actually moved the number, watches the dollar gap to benchmark shrink quarter over quarter, and gets the next winning lever named at exactly the moment the prior one is fixed.
