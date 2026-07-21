---
routine: lead-response-roi/quarterly-rerun
schedule: First Monday of each calendar quarter, 9am local
owner: Founder / Head of Sales / RevOps lead
---

# Routine: Quarterly Lead Response Re-Run

## Trigger
First Monday of each calendar quarter. Schedule via **`/schedule`** (Claude Code cloud routines, they keep firing after the session ends) after the initial Lead Response ROI diagnostic completes and the buyer wants to watch the leak shrink as they speed up response. If `/schedule` isn't available in the buyer's environment, fall back to the OS scheduler: launchd (macOS) or cron, invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

**Going live:** the routine is not live until it has fired once unattended. Agree on the check with the buyer at install time, the next quarter's re-run report appearing on its own (in `~/.claude/artemis/gtm-engineer/diagnostics/lead-response/artifacts/` or the agreed Slack channel), and only then call the trendline layer done.

The point of the re-run: a one-time diagnostic tells you what slow response costs you today. The quarterly re-run tells you whether the response time actually came down, whether the leak is shrinking, and whether the Speed-to-Lead build (if they ran it) is paying off. Response time drifts back up as volume grows and headcount changes. This catches the drift every 90 days.

## Preconditions

Required before scheduling, verify each at install time:

- **The initial diagnostic is complete and on disk.** `~/.claude/artemis/gtm-engineer/diagnostics/lead-response/` contains `discovery.json` (the baseline four inputs) and `response-time-ledger.md` with the baseline row. Verify: `ls ~/.claude/artemis/gtm-engineer/diagnostics/lead-response/response-time-ledger.md` returns the file and it contains the Baseline row. No baseline, no trendline, run the diagnostic first.
- **A path to fresh quarterly numbers.** Either a CRM connected to Claude (where the buyer can pull current response time and lead-to-opp rate) or the buyer's commitment to paste this quarter's numbers when the routine asks. Note which in `discovery.json`'s `connections` map.
- **A delivery channel (optional).** Slack or email connected if the buyer wants the re-run report pushed; otherwise it lands in the artifacts dir.

**Degraded mode:** if no fresh numbers are available when the routine fires (no connected tool, no buyer response), do not fabricate a reading, carry forward last quarter's values labeled "carried forward, not re-measured," write the report to `~/.claude/artemis/gtm-engineer/diagnostics/lead-response/artifacts/` anyway, and flag which inputs need real numbers next session. If Slack/email isn't connected, skip the push and surface the report at the next session open.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the quarterly Lead Response ROI re-run for [Company Name], comparing this quarter to the last recorded run. Read the prior state from ~/.claude/artemis/gtm-engineer/diagnostics/lead-response/ (discovery.json for the baseline four inputs, response-time-ledger.md for the trendline so far).

Before you re-measure anything, read the prior Prediction line in response-time-ledger.md, the recommendation made last time (the Speed-to-Lead Agent, or "no recommendation, at the ceiling") and the impact projected (the annual leak the fix should recover, the current-to-ceiling conversion lift, the weekly cost-of-delay). Hold it in mind; you are about to grade it against what actually happened.

Pull the same four inputs as the original diagnostic (pull directly from the connected CRM if one is wired, otherwise ask for paste-back):
1. Monthly inbound leads
2. Average deal size
3. Current average response time (map to one of the six HBR tiers: <5 min, 5-30 min, 30-60 min, 1-4 hours, 4-24 hours, 24+ hours)
4. Lead-to-opportunity rate, measured at the CURRENT response time

Then re-run the diagnostic methodology (playbook Step 4-5):
- Map the response time to its HBR multiplier (<5min 1.0 · 5-30min 0.4 · 30-60min 0.25 · 1-4h 0.15 · 4-24h 0.08 · 24+h 0.03).
- Treat the entered rate as currentConversion. Back-solve the ceiling: ceilingConversion = min(1, enteredRate ÷ multiplier). NEVER decay the entered rate twice.
- Compute revenueLostPerYear = (ceilingConversion − currentConversion) × monthlyLeads × dealSize × 12.
- Break the annual figure into cost-of-delay: weekly (÷52), monthly (÷12), daily (÷365).
- If the response time is now <5 min, or the entered rate is at/above the ceiling, flag OPTIMAL, the leak is closed.
- Diff against last quarter.

Output a TREND report, not a fresh diagnostic:

1. RESPONSE TIME TREND
 - This quarter's tier vs last quarter vs the original baseline.
 - Show the trajectory: e.g., "Baseline 24+ hours → Q1 1-4 hours → Q2 30-60 min. Two tiers faster in two quarters."
 - Flag if the response time got SLOWER; that's a regression, name it loudly.

2. LEAK TREND
 - This quarter's annual leak vs last quarter vs baseline, with the calculation shown.
 - "Leak was $3.4M/year at 1-4 hours; you moved to 30-60 min and it's now $1.8M/year. That's ~$1.6M/year recovered. The Speed-to-Lead work held."
 - If the leak is now $0 (OPTIMAL), celebrate it plainly: "You're at the sub-5-minute ceiling. The response-time leak is closed."

3. WHAT MOVED IT
 - Did response time improve, conversion rate improve, or volume/deal size change the figure? Attribute the shift honestly. A bigger leak because lead volume doubled is a growth signal, not a regression.

4. STILL OPEN (if the leak persists)
 - Re-quantify at current numbers (the $ figure moves as volume/deal size change).
 - Do NOT pitch a bundle, this is a single-leak diagnostic. If the buyer mentions problems beyond response time, hand back to the shell's full GTM diagnostic, don't try to quantify those here.

5. RECOMMENDED NEXT MOVE
 - If the leak is shrinking and response time is improving, say so plainly: "Response time is trending down, the leak is shrinking. Next quarter, target the [next faster] tier."
 - If the leak is closed (OPTIMAL), recommend nothing to buy, tell them to hold the pace as volume scales, and point to a different diagnostic only if another part of the funnel is clearly the real leak.

6. PREDICTION CALIBRATION (grade last quarter's call)
 - Read the prior Prediction line you held from the ledger, then answer three things in one human line: did the buyer act on the recommendation (did they ship the Speed-to-Lead Agent / wire the speed-to-lead fix, or not), did the metric actually move (did response time drop a tier and did lead-to-opp climb toward the projected ceiling), and how the projected dollar compares to the actual shift (projected-versus-actual). Close with one sentence of advisor judgment. Keep it to a line, e.g.: "Projected a $3.4M/year recovery if they shipped Speed-to-Lead; they wired Warmly + Amplemarket, response time moved 1-4 hours to 30-60 min, lead-to-opp climbed into the projected band and the leak is down ~$1.6M/year, the decay-curve call held." If they did NOT act: "Recommended Speed-to-Lead; they haven't shipped it, response time held at [tier] and the leak is unchanged, the projection stands and so does the weekly cost-of-delay." If it was OPTIMAL last quarter: "Predicted $0 holds unless response slips; it [held / slipped to [tier] as volume grew], the call [was right / needs the fix re-surfaced]." Note the multipliers stay fixed, this grades the realized outcome, it does not re-improvise the decay curve.

Respect the no-leak rule: if the buyer is at the ceiling, recommend nothing. Never invent a number the buyer didn't provide this quarter. Only quantify with the buyer's current numbers or named benchmark substitutions. If fresh numbers aren't available, carry forward last quarter's values labeled "carried forward, not re-measured" rather than fabricating a reading. And never decay the entered conversion rate twice.

Write the outputs to disk: append this quarter's row to ~/.claude/artemis/gtm-engineer/diagnostics/lead-response/response-time-ledger.md, append the one-line Prediction Calibration (section 6 above) right under it, then write this quarter's fresh Prediction line for next quarter to grade. Save the full trend report to ~/.claude/artemis/gtm-engineer/diagnostics/lead-response/artifacts/rerun-[YYYY-Qn].md. Then post the trend summary to the buyer's preferred channel (Slack #revenue-ops or email) if one is connected; if not, the artifacts copy is the deliverable. Lead with the trajectory and the headline: "[Company] lead-response leak: [$X]/year ([+/-X] vs last quarter), response time now [tier]."
```

## The ledger (the durable trendline)

The trend lives in one named file: **`~/.claude/artemis/gtm-engineer/diagnostics/lead-response/response-time-ledger.md`**. The initial diagnostic writes the Baseline row at close; this routine appends one row per quarter. The ledger is the source of truth, the trend is read from it, never re-derived from memory:

| Quarter | Response Tier | Current Conv | Ceiling Conv | Annual Leak | Weekly Cost-of-Delay |
|---------|---------------|--------------|--------------|-------------|----------------------|
| Baseline | | | | | |
| Q1 | | | | | |
| Q2 | | | | | |

A healthy trajectory: response tier getting faster, the gap between current and ceiling conversion closing, the annual leak shrinking toward $0, and weekly cost-of-delay falling. When the tier reaches <5 min, the leak is closed and the ledger reads OPTIMAL.

The same ledger also carries the prediction-calibration trail (the diagnostic-calibration field in `results-memory.md`, "If you are a free diagnostic"). The initial diagnostic wrote a dated Prediction line under the baseline row (what was recommended, what was projected). Each quarter this routine reads that line first, then appends one Prediction Calibration line under the new row (did they act, did the metric move, projected-versus-actual, one sentence of judgment), and writes a fresh Prediction line for next quarter to grade. Keep these as human one-liners beneath the table, not extra columns; the calibration is the learning, not a spreadsheet.

## Anti-hallucination

Re-measure only inputs the buyer re-provides this quarter. If they don't have a fresh number for a field, carry forward last quarter's value and label it "carried forward, not re-measured"; don't fabricate a new reading. Never decay the entered conversion rate twice. The leak trend is only credible if every point on it traces to real inputs.

## What success looks like

The buyer has a 90-day cadence that shows the lead-response leak as a line going down and to the right, confirms the Speed-to-Lead build paid off (or that response time is improving on its own), catches response-time regressions the quarter they happen, and re-surfaces the Speed-to-Lead Agent only if the leak is still material and unfixed. When the leak hits $0, the routine says so and recommends nothing, the diagnostic's whole point is to tell the truth, including the good news.
