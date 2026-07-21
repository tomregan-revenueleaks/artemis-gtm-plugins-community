---
routine: gtm-engineer/diagnose-quarterly-reaudit
schedule: First Monday of each calendar quarter, 9am local
owner: Founder / Head of GTM / RevOps lead
---

# Routine: Quarterly GTM Re-Audit (DIAGNOSE mode)

## Trigger
First Monday of each calendar quarter. Schedule via **`/schedule`** (Claude Code cloud routines, they keep firing after the session ends) after the initial GTM Audit completes and the buyer wants to track the health-score trend over time. If `/schedule` isn't available in the buyer's environment, fall back to the OS scheduler: launchd (macOS) or cron, invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

**Going live:** the routine is not live until it has fired once unattended. Agree on the check with the buyer at install time, the next quarter's trend report appearing on its own (in `~/.claude/artemis/gtm-engineer/diagnostics/artifacts/` or the agreed Slack channel), and only then call the trendline layer done.

The point of the re-audit: a one-time audit tells you where you're leaking today. The quarterly re-run tells you whether the leaks you fixed stayed fixed, whether the health score is climbing, and whether new leaks opened up as the company grew. GTM health drifts. This catches the drift every 90 days.

## Preconditions

Required before scheduling, verify each at install time:

- **The initial audit is complete and on disk.** `~/.claude/artemis/gtm-engineer/diagnostics/` contains `discovery.json` (the baseline inputs) and `~/.claude/artemis/gtm-engineer/ledgers/health-score-ledger.md` carries the baseline row. Verify: `ls ~/.claude/artemis/gtm-engineer/ledgers/health-score-ledger.md` returns the file and it contains the Baseline row. No baseline, no trendline, run the audit first.
- **A path to fresh quarterly numbers.** Either a CRM/engagement tool connected to Claude (see `tool-connections.md`) or the buyer's commitment to paste this quarter's metrics when the routine asks. Note which in `discovery.json`'s `connections` map.
- **A delivery channel (optional).** Slack or email connected if the buyer wants the trend report pushed; otherwise it lands in the artifacts dir.

**Degraded mode:** if no fresh numbers are available when the routine fires (no connected tool, no buyer response), do not fabricate a reading, carry forward last quarter's values labeled "carried forward, not re-measured," write the report to `~/.claude/artemis/gtm-engineer/diagnostics/artifacts/` anyway, and flag which metrics need real numbers next session. If Slack/email isn't connected, skip the push and surface the report at the next session open.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the quarterly GTM re-audit for [Company Name], comparing this quarter to the last recorded audit. Read the prior state from ~/.claude/artemis/gtm-engineer/ (diagnostics/discovery.json for the baseline inputs, ledgers/health-score-ledger.md for the trendline so far), and the shared profile at ~/.claude/artemis/_profile/profile.json.

Pull the same inputs as the original GTM Audit (use the discovery-questions template; pull directly from the connected CRM per tool-connections.md if one is wired, otherwise ask for paste-back):
1. Company profile, industry, size, growth motion (re-confirm motion; companies shift PLG→Hybrid as they add sales)
2. Revenue metrics, deal size, sales cycle, monthly leads, win rate, pipeline coverage, lead-to-opp
3. Team & growth, motion-specific (SLG: SDR/AE count, quota attainment; PLG: trial-to-paid, activation, expansion)
4. Signal-stack, what intent/visitor tools they now run (re-grade Gold/Silver/Bronze/No-Tools)

Then re-run the audit methodology (playbook Steps 5-7):
- Re-benchmark every metric against the industry × motion table (>1.3x above avg, <0.7x leaking)
- Recompute the overall GTM health score (0-100) and signal tier
- Re-quantify each surviving leak as $X/year
- Diff against last quarter

Output a TREND report, not a fresh audit:

1. HEALTH SCORE TREND
 - This quarter's score vs last quarter vs the original baseline
 - Show the trajectory: e.g., "Baseline 54 → Q1 61 → Q2 68. +14 in two quarters, on track."
 - Flag if the score DROPPED; that's a regression, name it loudly.

2. LEAKS CLOSED
 - Which leaks from last quarter are now at/above benchmark? Name them, celebrate them.
 - "Lead-to-opp moved 9% → 16%, now at benchmark. The qualification-automation work held. That's ~$X/year recovered."

3. LEAKS STILL OPEN
 - Which leaks persist? Re-quantify at current numbers (the $ figure moves as volume/deal size change).
 - Flag any leak that got WORSE quarter-over-quarter.

4. NEW LEAKS
 - Metrics that were healthy last quarter and crossed below 0.7x this quarter.
 - These are usually growth-induced: more traffic exposed a slow-response gap, more signups exposed an activation gap, a new motion exposed an outbound gap.
 - For each new leak, map to the matching build module + partner (playbook Step 7).

5. RECOMMENDED NEXT MOVE
 - Rank open + new leaks by current $ impact.
 - If 3+ leaks are open, route them to the roadmap and name the modules in priority order (see playbook Step 7). Owned modules start this week; any unowned module gets the honest gate (what it builds and the buyer's own leak math), with its price quoted at runtime from module-manifest.json, never written here.
 - If the score is climbing and leaks are closing, say so plainly: "GTM health is trending up. Next quarter, focus on [single highest-impact open leak]."

6. PREDICTION CALIBRATION (grade last quarter's call)
 - Read back the prior prediction you loaded at the top: the fix recommended and the projected $/yr recovery.
 - Write one human line of advisor judgment, the way the results-memory note frames it. For example: "Projected ~$310K/yr recovered if they shipped Speed-to-Lead for the response leak; they shipped it, median response dropped under 5 minutes, and the leak closed close to the projected band, the call held." Or, when it misses: "Projected recovery from Lead Scoring; they bought it but win rate barely moved, the leak was upstream in ICP, recalibrate, score the ICP leak ahead of scoring next time." Keep it to one line, no sample sizes, no confidence intervals.
 - If they did NOT act on the recommendation, say so plainly and carry the prediction forward rather than grading it as a miss: "Recommended Speed-to-Lead last quarter; not yet built, so the response leak is still open at [$X]; the projection stands until they ship."

Respect the motion guardrail: PLG re-audits stay on PLG metrics, SLG on sales metrics. Never invent a metric the buyer didn't provide this quarter. Only quantify with the buyer's current numbers or named benchmark substitutions. If fresh numbers aren't available, carry forward last quarter's values labeled "carried forward, not re-measured" rather than fabricating a reading.

Write the outputs to disk: append this quarter's row to ~/.claude/artemis/gtm-engineer/ledgers/health-score-ledger.md (including this quarter's calibration read on last quarter's prediction AND the new recommended fix and projected $/yr recovery you're making this quarter) and save the full trend report to ~/.claude/artemis/gtm-engineer/diagnostics/artifacts/reaudit-[YYYY-Qn].md. Then post the trend summary to the buyer's preferred channel (Slack #revenue-ops or email) if one is connected; if not, the artifacts copy is the deliverable. Lead with the score trajectory and the headline: "[Company] GTM health: [score] ([+/-X] vs last quarter)."
```

## The ledger (the durable trendline)

The trend lives in one named file: **`~/.claude/artemis/gtm-engineer/ledgers/health-score-ledger.md`**. The initial audit writes the Baseline row at close; this routine appends one row per quarter. The ledger is the source of truth, the trend is read from it, never re-derived from memory. Each row also carries the prediction that audit made (the recommended fix and the projected $/yr recovery) and, from this routine on, a one-line calibration read on the PRIOR row's prediction (did they act, did the metric move, projected versus actual):

| Quarter | Health Score | Signal Tier | Leaks Open | Total $ Impact | Top Leak | Recommended Fix | Projected $/yr Recovery | Calibration (prior prediction) |
|---------|--------------|-------------|------------|----------------|----------|-----------------|-------------------------|--------------------------------|
| Baseline | | | | | | | | (none, baseline sets the first prediction) |
| Q1 | | | | | | | | |
| Q2 | | | | | | | | |

A healthy trajectory: score climbing, leak count falling, total $ impact shrinking, signal tier moving up (No-Tools → Bronze → Silver → Gold), and the calibration column showing the recommended fixes landing where you projected. The Baseline row sets the first prediction (it has nothing prior to grade); every later row grades the prediction one row above it and sets its own.

## Anti-hallucination

Re-benchmark only metrics the buyer re-provides this quarter. If they don't have fresh numbers for a field, carry forward last quarter's value and label it "carried forward, not re-measured"; don't fabricate a new reading. The health-score trend is only credible if every point on it traces to real inputs.

## What success looks like

The buyer has a 90-day cadence that shows GTM health as a line going up and to the right, catches new leaks the quarter they open (not the year they cost real money), and re-surfaces the next module in the roadmap at exactly the moment the data justifies it.
