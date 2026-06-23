---
routine: quota-gap-diagnostic/quarterly-recheck
schedule: First Monday of each calendar quarter, 9am local
owner: Sales leader / Founder / RevOps lead
---

# Routine: Quarterly Quota Coverage Re-Check (Artemis Quota Line)

## Trigger
First Monday of each calendar quarter — the start of a fresh number. Schedule via **`/schedule`** (Claude Code cloud routines — they keep firing after the session ends) after the initial Quota Gap Diagnostic completes and the buyer wants to track coverage over time. If `/schedule` isn't available in the buyer's environment, fall back to the OS scheduler: launchd (macOS) or cron, invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

**Going live:** the routine is not live until it has fired once unattended. Agree on the check with the buyer at install time — the next quarter's re-check appearing on its own (in `~/.claude/artemis/quota-gap-diagnostic/artifacts/` or the agreed Slack channel) — and only then call the trendline layer done.

The point of the re-check: a one-time diagnostic tells you whether you'll hit THIS quarter. The quarterly re-run tells you whether the fix landed, whether coverage is climbing toward what your win rate requires, and whether the diagnosis flipped (a volume gap you fixed can become a capacity problem once the pipeline shows up). Coverage drifts every quarter as the number resets and the pipeline turns over. This catches the drift at the start of each one, while there's still time to act.

## Preconditions

Required before scheduling — verify each at install time:

- **The initial diagnostic is complete and on disk.** `~/.claude/artemis/quota-gap-diagnostic/` contains `discovery.json` (the baseline inputs) and `coverage-ledger.md` with the baseline row. Verify: `ls ~/.claude/artemis/quota-gap-diagnostic/coverage-ledger.md` returns the file and it contains the Baseline row. No baseline, no trendline — run the diagnostic first.
- **A path to fresh quarterly numbers.** Either a CRM connected to Claude (HubSpot / Salesforce / Attio with a pipeline report) or the buyer's commitment to paste this quarter's target, win rate, deal size, and current pipeline when the routine asks. Note which in `discovery.json`'s `connections` map.
- **A delivery channel (optional).** Slack or email connected if the buyer wants the re-check pushed; otherwise it lands in the artifacts dir.

**Degraded mode:** if no fresh numbers are available when the routine fires (no connected tool, no buyer response), do not fabricate a reading — carry forward last quarter's values labeled "carried forward, not re-measured," write the re-check to `~/.claude/artemis/quota-gap-diagnostic/artifacts/` anyway, and flag which inputs need real numbers next session. If Slack/email isn't connected, skip the push and surface the re-check at the next session open.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the quarterly quota coverage re-check for [Company / Team], comparing this quarter to the last recorded run. Read the prior state from ~/.claude/artemis/quota-gap-diagnostic/ (discovery.json for the baseline inputs, coverage-ledger.md for the trendline so far).

Pull the same inputs as the original Quota Gap Diagnostic (use the discovery-questions template; pull directly from the connected CRM if one is wired, otherwise ask for paste-back):
1. Targets & team — annual (or quarterly) revenue target, number of AEs, average deal size, win rate, sales cycle (re-confirm win rate; it drives required coverage)
2. Current pipeline — total open opportunity value (re-confirm raw vs win-rate-weighted), days left in the new quarter, lead-to-opp rate
3. Then re-run the gap math (playbook Phases 3-6):
 - requiredPipelineQuarterly = (annualTarget / 4) / (winRate/100); requiredCoverage = 1 / (winRate/100)
 - coverageRatio = currentPipeline / quarterlyTarget; grade it as a fraction of requiredCoverage
 - pipelineGap = requiredPipelineQuarterly − currentPipeline (net of current pipeline)
 - incremental monthly activity (leads/opps/deals); AE capacity (active opps/AE vs the 25 ceiling); time-to-close cutoff
 - re-diagnose via the precedence: capacity → conversion → volume → on-track
 - cost-of-delay on the annualized gap

Output a TREND report, not a fresh diagnostic:

1. COVERAGE TREND
 - This quarter's coverage ratio and grade vs last quarter vs the original baseline
 - Show the trajectory: e.g., "Baseline 1.8x (D) → Q1 2.6x (C) → Q2 3.4x (B). Climbing toward the 4.5x your win rate needs."
 - Flag if coverage DROPPED or the grade fell; that's a regression, name it loudly.

2. GAP MOVEMENT
 - This quarter's true gap (net of pipeline) vs last quarter. Did the dollar gap shrink?
 - "Gap was $1.27M, now $480K. The volume fix is working — $790K of pipeline added since last quarter."
 - If the gap GREW, name it and ask what changed (target raised? win rate slipped? pipeline turned over without replacement?).

3. DIAGNOSIS SHIFT
 - Did the primary problem change? A fixed volume gap can surface a capacity problem (pipeline showed up, AEs now buried) or a conversion problem (more opps, same low win rate).
 - For any new primary problem, map it to the matching Artemis agent + partner (playbook Phase 6 fix table). Volume → Outbound System; conversion → Conversion Content System; capacity → Qualification Automation.
 - If the diagnosis is now on-track, say it plainly and celebrate it.

4. WHAT TO WATCH THIS QUARTER
 - The one number whose movement decides the quarter (usually win rate or the rate new pipeline is entering).
 - The time-to-close cutoff date for the new quarter — the last day a new deal can be created and still close.

5. RECOMMENDED NEXT MOVE
 - If a gap is still open, name the ONE matching agent (one diagnosis, one agent — never a bundle off a single quota gap).
 - If coverage is climbing and the gap is closing, say so plainly: "Coverage is trending up and the gap is closing. Stay the course; re-check next quarter."

Respect the diagnosis precedence (capacity → conversion → volume → on-track). Never invent a number the buyer didn't provide this quarter. Only quantify with the buyer's current numbers or named benchmark substitutions. If fresh numbers aren't available, carry forward last quarter's values labeled "carried forward, not re-measured" rather than fabricating a reading.

Write the outputs to disk: append this quarter's row to ~/.claude/artemis/quota-gap-diagnostic/coverage-ledger.md and save the full trend report to ~/.claude/artemis/quota-gap-diagnostic/artifacts/recheck-[YYYY-Qn].md. Then post the trend summary to the buyer's preferred channel (Slack #revenue-ops or email) if one is connected; if not, the artifacts copy is the deliverable. Lead with the coverage trajectory and the headline: "[Company] quota coverage: [X.X]x ([+/-X.X]x vs last quarter), gap [$X] ([up/down] [$X])."
```

## The ledger (the durable trendline)

The trend lives in one named file: **`~/.claude/artemis/quota-gap-diagnostic/coverage-ledger.md`**. The initial diagnostic writes the Baseline row at close; this routine appends one row per quarter. The ledger is the source of truth — the trend is read from it, never re-derived from memory:

| Quarter | Annual Target | Win Rate | Coverage | Grade | Quarterly Gap | Primary Problem | Days to Cutoff |
|---------|---------------|----------|----------|-------|---------------|-----------------|----------------|
| Baseline | | | | | | | |
| Q1 | | | | | | | |
| Q2 | | | | | | | |

A healthy trajectory: coverage ratio climbing toward (and past) the win-rate requirement, grade rising, the dollar gap shrinking, the diagnosis moving toward on-track.

## Anti-hallucination

Re-compute only off numbers the buyer re-provides this quarter. If they don't have a fresh figure for a field, carry forward last quarter's value and label it "carried forward, not re-measured"; don't fabricate a new reading. The coverage trend is only credible if every point on it traces to real inputs. Required pipeline chains off win rate — if the win rate is carried forward, say the gap is directional this quarter.

## What success looks like

The buyer has a quarterly cadence that shows quota coverage as a line climbing toward what their win rate requires, sizes the true gap (net of pipeline) each quarter while there's still time to act, catches a diagnosis flip the quarter it happens (not the quarter it costs the number), and re-surfaces the one matching agent at exactly the moment the math justifies it.
