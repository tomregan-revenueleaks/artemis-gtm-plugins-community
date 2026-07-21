---
routine: deanonymization-roi-agent/quarterly-rerun
schedule: First Monday of each calendar quarter, 9am local
owner: Founder / Head of GTM / RevOps / Demand-gen lead
---

# Routine: Quarterly Visitor-Pipeline Re-Run

## Trigger
First Monday of each calendar quarter. Schedule via **`/schedule`** (Claude Code cloud routines, they keep firing after the session ends) after the initial Website Visitor ROI Calculator run completes and the buyer wants to track the hidden-pipeline figure as their traffic grows. If `/schedule` isn't available in the buyer's environment, fall back to the OS scheduler: launchd (macOS) or cron, invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

**Going live:** the routine is not live until it has fired once unattended. Agree on the check with the buyer at install time, the next quarter's pipeline re-read appearing on its own (in `~/.claude/artemis/gtm-engineer/diagnostics/deanonymization-roi/artifacts/` or the agreed Slack channel), and only then call the trendline layer done.

The point of the re-run: a one-time run tells you what your anonymous traffic is worth today. The quarterly re-run tells you whether the figure is growing as traffic grows, and, once the buyer has installed identification, swaps the benchmark identification rate for their REAL measured rate, so the figure stops being an estimate and becomes a measurement. Two things change quarter to quarter: traffic volume, and whether a tool is now installed. This catches both every 90 days.

## Preconditions

Required before scheduling, verify each at install time:

- **The initial run is complete and on disk.** `~/.claude/artemis/gtm-engineer/diagnostics/deanonymization-roi/` contains `discovery.json` (the baseline traffic + ACV) and `pipeline-ledger.md` with the baseline row. Verify: `ls ~/.claude/artemis/gtm-engineer/diagnostics/deanonymization-roi/pipeline-ledger.md` returns the file and it contains the Baseline row. No baseline, no trendline, run the calculator first.
- **A path to fresh quarterly traffic numbers.** Either an analytics/CRM tool connected to Claude (see `tool-connections.md`) or the buyer's commitment to paste this quarter's monthly visitors and ACV when the routine asks. Note which in `discovery.json`'s `connections` map.
- **(After a tool is installed) the real identification rate.** Once the buyer has installed Warmly or RB2B, the routine should read the tool's actual identification rate (from the tool's dashboard, pasted or pulled) and use it instead of the 15%/22%/38% benchmark. Note in `discovery.json` whether a tool is live yet.
- **A delivery channel (optional).** Slack or email connected if the buyer wants the figure pushed; otherwise it lands in the artifacts dir.

**Degraded mode:** if no fresh numbers are available when the routine fires (no connected tool, no buyer response), do not fabricate a reading, carry forward last quarter's traffic and ACV labeled "carried forward, not re-measured," re-run the funnel on those, write the report to `~/.claude/artemis/gtm-engineer/diagnostics/deanonymization-roi/artifacts/` anyway, and flag which inputs need real numbers next session. If Slack/email isn't connected, skip the push and surface the report at the next session open.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the quarterly Website Visitor ROI re-run for [Company Name], comparing this quarter to the last recorded run. Read the prior state from ~/.claude/artemis/gtm-engineer/diagnostics/deanonymization-roi/ (discovery.json for the baseline traffic + ACV, pipeline-ledger.md for the trendline so far).

BEFORE you re-measure, read the prior prediction off the most recent pipeline-ledger.md row: the recommendation that was made (the anonymous-traffic leak as the named lever, the Beacon agent + the partner tool that was recommended, or the "grow traffic first, no tool recommended" call) and the projected impact (the identification rate that was projected and the conservative expected recoverable pipeline it implied). Hold that prediction in mind, you are about to grade it against what actually happened.

Pull the same inputs as the original run (use the discovery-questions template; pull directly from the connected analytics/CRM per tool-connections.md if one is wired, otherwise ask for paste-back):
1. Traffic profile, monthly visitors, source mix, ICP-fit share (re-confirm the mix; traffic shifts as marketing changes)
2. Deal size, average ACV (re-confirm; deal size drifts as the company moves up-market)
3. Tool status, has the buyer installed Warmly or RB2B since last quarter? If yes, get the tool's REAL measured identification rate and use it instead of the benchmark.

Then re-run the funnel methodology (playbook Phase 3-4):
- ICP-fit-adjust the visitor count
- Apply the identification rate: the tool's REAL rate if installed, otherwise the benchmark for the traffic mix (15% floor / 22% Warmly / 38% RB2B US)
- Run the account-level funnel (meetings off accounts, contacts as effort only)
- Recompute expected and upper-bound annual pipeline
- Diff against last quarter

Output a TREND report, not a fresh run:

1. PIPELINE TREND
 - This quarter's expected figure vs last quarter vs the original baseline
 - Show the trajectory: e.g., "Baseline $1.8M → Q1 $2.2M → Q2 $2.7M. +$900K in two quarters as ICP-fit traffic grew."
 - Flag if the figure DROPPED (traffic fell, or ICP-fit share thinned); name it.

2. WHAT CHANGED
 - Traffic: up/down, and the pipeline impact (the figure scales roughly linearly with ICP-fit traffic).
 - Deal size: any ACV change and its effect.
 - Identification rate: if a tool is now installed, note the switch from benchmark to REAL measured rate, and whether reality beat or missed the benchmark, that's the single most valuable data point in the trend.

3. CAPTURE CHECK (only once a tool is installed)
 - Is the tool actually surfacing the accounts the funnel predicted? Compare the tool's real identified-account count to the funnel's prediction. If the tool is underperforming the benchmark, flag it, it may be a setup issue (ICP filters too tight, script not firing on all pages) worth the Beacon agent's attention.

4. RECOMMENDED NEXT MOVE
 - If no tool is installed yet and the figure is growing, restate the case: "Your anonymous-traffic pipeline is now [$X]/year and climbing; the Visitor Deanonymization module (`modules/visitor-id/`) captures it, the shell places it on the roadmap and quotes the price."
 - If a tool IS installed and underperforming, point at the Visitor Deanonymization module for a tune-up, not a fresh install.
 - If the figure is climbing and capture is working, say so plainly: "Identification is paying off; next quarter, focus on [growing ICP-fit traffic / tightening the high-intent routing]."

5. PREDICTION CALIBRATION (grade the prior prediction)
 - Reconcile the prior prediction you read at the top against what actually happened, and append ONE calibration row (the format below). Answer three things in that one line: did the buyer act on the recommendation (did they install the Visitor Deanonymization module / Warmly / RB2B, or build the fix), did the metric actually move (did identified pipeline materialize, and how did the tool's REAL identification rate compare to the rate you projected), and the projected-versus-actual read against the conservative floor. Close with one sentence of advisor judgment, human, one line.
 - Examples of the voice: "Projected ~$1.8M recoverable at a 22% Warmly rate if they installed; they installed and the real rate landed at 19%, just under the floor projection but the recoverable pipeline still materialized, the leak call held." Or: "Recommended growing ICP-fit traffic before any tool; traffic climbed past the threshold this quarter, so the no-tool call has expired, the Visitor Deanonymization recommendation is now live." Or, honestly: "Recommended the Visitor Deanonymization module last quarter; they haven't installed, so the projection is untested, restating the case at the same conservative rate."

Respect the scope guardrail: this is the anonymous-traffic figure only. If a different leak surfaces (slow response on identified accounts, no routing), note it and point at the free GTM Audit, do not quantify it here. Never invent traffic or deal size; only quantify off the buyer's current numbers, the tool's real rate, or named benchmark substitutions. If fresh numbers aren't available, carry forward last quarter's values labeled "carried forward, not re-measured" rather than fabricating a reading.

Write the outputs to disk: append this quarter's row (the trend numbers plus the one-line prediction calibration) to ~/.claude/artemis/gtm-engineer/diagnostics/deanonymization-roi/pipeline-ledger.md and save the full trend report to ~/.claude/artemis/gtm-engineer/diagnostics/deanonymization-roi/artifacts/rerun-[YYYY-Qn].md. Then post the trend summary to the buyer's preferred channel (Slack #revenue-ops or email) if one is connected; if not, the artifacts copy is the deliverable. Lead with the figure trajectory and the headline: "[Company] anonymous-traffic pipeline: [$X]/year ([+/-$Y] vs last quarter)."
```

## The ledger (the durable trendline)

The trend lives in one named file: **`~/.claude/artemis/gtm-engineer/diagnostics/deanonymization-roi/pipeline-ledger.md`**. The initial run writes the Baseline row at close, carrying both the metric snapshot AND the prediction (the recommendation made and the projected impact); this routine appends one row per quarter, and each quarterly row also carries the one-line prediction calibration that grades the prior row's prediction. The ledger is the source of truth, the trend is read from it, never re-derived from memory:

| Quarter | Monthly Visitors | ICP-Fit % | ACV | ID Rate (basis) | Expected Pipeline | Upper Bound | Recommendation + Projected Impact | Prediction Calibration |
|---------|------------------|-----------|-----|-----------------|-------------------|-------------|-----------------------------------|------------------------|
| Baseline | | | | | | | leak + recommended agent/tool, projected ID rate + recoverable pipeline | (set at next re-run) |
| Q1 | | | | | | | | did they act, did pipeline materialize vs the floor, one line of judgment |
| Q2 | | | | | | | | |

A healthy trajectory: ICP-fit traffic climbing, expected pipeline climbing, and, once a tool is installed, the ID Rate column switching from a benchmark to a REAL measured rate that meets or beats the benchmark. The Recommendation + Projected Impact column is the prediction the next re-run reconciles; the Prediction Calibration column is where it records whether the buyer acted and whether the number moved as projected.

## Anti-hallucination

Re-run only on traffic and deal size the buyer re-provides this quarter (or a tool's real measured rate). If they don't have fresh numbers, carry forward last quarter's values and label them "carried forward, not re-measured"; don't fabricate a new reading. The pipeline trend is only credible if every point on it traces to real inputs. Keep counting meetings at the account level and keep pipeline separate from closed revenue, exactly as the playbook requires.

## What success looks like

The buyer has a 90-day cadence that shows their anonymous-traffic pipeline as a line growing with their traffic, switches from estimate to measurement the quarter they install a tool, catches a tool underperforming its benchmark while it's still a setup fix, and re-surfaces the Beacon agent at exactly the moment the data justifies it.
