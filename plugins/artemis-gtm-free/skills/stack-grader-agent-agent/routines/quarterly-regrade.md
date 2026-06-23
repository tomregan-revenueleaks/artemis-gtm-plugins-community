---
routine: stack-grader/quarterly-regrade
schedule: First Monday of each calendar quarter, 9am local
owner: Founder / Head of GTM / RevOps lead
---

# Routine: Quarterly Stack Re-Grade (Artemis Inventory)

## Trigger
First Monday of each calendar quarter. Schedule via **`/schedule`** (Claude Code cloud routines — they keep firing after the session ends) after the initial Stack Grader completes and the buyer wants to track the Coverage Score trend over time. If `/schedule` isn't available in the buyer's environment, fall back to the OS scheduler: launchd (macOS) or cron, invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

**Going live:** the routine is not live until it has fired once unattended. Agree on the check with the buyer at install time — the next quarter's trend report appearing on its own (in `~/.claude/artemis/stack-grader/artifacts/` or the agreed Slack channel) — and only then call the trendline layer done.

The point of the re-grade: a one-time grade tells you where your stack stands today. The quarterly re-run tells you whether the gaps you filled stayed filled, whether the redundancies you cut stayed cut, whether the Coverage Score is climbing, and whether new sprawl crept back in as the company added tools. Stacks drift — a team adds a second enrichment tool in a hurry, lets a category go shelfware, or scales into a stage where new categories become must-haves. This catches the drift every 90 days.

## Preconditions

Required before scheduling — verify each at install time:

- **The initial grade is complete and on disk.** `~/.claude/artemis/stack-grader/` contains `discovery.json` (the baseline inventory) and `coverage-score-ledger.md` with the baseline row. Verify: `ls ~/.claude/artemis/stack-grader/coverage-score-ledger.md` returns the file and it contains the Baseline row. No baseline, no trendline — run the grade first.
- **A path to the fresh quarterly stack.** Either a CRM/billing tool connected to Claude (see `tool-connections.md`) or the buyer's commitment to paste this quarter's tool list when the routine asks. Note which in `discovery.json`'s `connections` map.
- **A delivery channel (optional).** Slack or email connected if the buyer wants the trend report pushed; otherwise it lands in the artifacts dir.

**Degraded mode:** if no fresh inventory is available when the routine fires (no connected tool, no buyer response), do not fabricate a stack — carry forward last quarter's inventory labeled "carried forward, not re-inventoried," write the report to `~/.claude/artemis/stack-grader/artifacts/` anyway, and flag that the inventory needs a real refresh next session. If Slack/email isn't connected, skip the push and surface the report at the next session open.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the quarterly Sales Tech Stack re-grade for [Company Name], comparing this quarter to the last recorded grade. Read the prior state from ~/.claude/artemis/stack-grader/ (discovery.json for the baseline inventory, coverage-score-ledger.md for the trendline so far).

Pull the same inputs as the original Stack Grader (use the discovery-questions template; pull directly from the connected CRM/billing tool per tool-connections.md if one is wired, otherwise ask for paste-back):
1. Company profile — re-confirm growth motion (PLG/SLG/Hybrid) and stage (early/growth/scale); companies cross stage boundaries and add a sales motion, which changes both the weights and the must/should/nice map.
2. Stack inventory — re-walk all 8 categories (CRM, Sales Engagement, Data Enrichment, Conversation Intelligence, Visitor ID, Revenue Intelligence, Pipeline Management, Sales Enablement). Capture every tool, including unlisted ones. Note new tools added and old tools cut since last quarter.

Then re-run the grade methodology (playbook Steps 4-6):
- Re-apply the motion weights, sum raw coverage, subtract redundancy drag (capped 15), clamp 0-100.
- Re-assign the letter grade and maturity tier.
- Re-classify gaps by stage (Critical/Important/Nice) — remember a stage change can turn a former nice-to-have into a must-have.
- Re-price each open gap (stage ARR × leak rate) and each redundancy (duplicate waste).
- Diff against last quarter.

Output a TREND report, not a fresh grade:

1. COVERAGE SCORE TREND
 - This quarter's score vs last quarter vs the original baseline.
 - Show the trajectory: e.g., "Baseline 62 → Q1 71 → Q2 78. +16 in two quarters, on track."
 - Flag if the score DROPPED; that's a regression, name it loudly (usually a new redundancy or a stage change that opened a new must-have category).

2. GAPS CLOSED
 - Which categories that were gaps last quarter are now covered? Name them, celebrate them.
 - "Visitor ID went from a 22-point PLG gap to covered (Warmly). That's ~$400K/year of forgone pipeline recovered and 22 points added to the score."

3. GAPS STILL OPEN
 - Which categories are still gaps? Re-price at the current stage (the $ figure moves if the company changed stage).
 - Flag any gap that got more expensive because the company scaled into a higher stage.

4. NEW SPRAWL / NEW GAPS
 - Categories that crossed into 3+ tools this quarter (new redundancy drag), or categories that became must-haves because the company changed stage and are now uncovered.
 - For each, re-price and map to the fix (Tech Stack Audit Agent + partner per category, playbook Step 6).

5. RECOMMENDED NEXT MOVE
 - Rank open gaps by current $ stake and redundancies by current waste.
 - If gaps or redundancies remain, surface the Tech Stack Audit Agent ($349) as the engagement that sequences the fix (see playbook cross-sell; no bundle ladder — one paid agent).
 - If the score is climbing and gaps are closing, say so plainly: "Stack coverage is trending up. Next quarter, focus on [single highest-stake open gap / costliest redundancy]."

Respect the motion and stage guardrails: re-weight against the current motion column, re-classify against the current stage map. Count unlisted tools. Never invent a tool the buyer didn't name this quarter. Only price with the buyer's current stage or a labeled stage mid-point. If fresh inventory isn't available, carry forward last quarter's stack labeled "carried forward, not re-inventoried" rather than fabricating one.

Write the outputs to disk: append this quarter's row to ~/.claude/artemis/stack-grader/coverage-score-ledger.md and save the full trend report to ~/.claude/artemis/stack-grader/artifacts/regrade-[YYYY-Qn].md. Then post the trend summary to the buyer's preferred channel (Slack #revenue-ops or email) if one is connected; if not, the artifacts copy is the deliverable. Lead with the score trajectory and the headline: "[Company] stack coverage: [score]/100 ([+/-X] vs last quarter)."
```

## The ledger (the durable trendline)

The trend lives in one named file: **`~/.claude/artemis/stack-grader/coverage-score-ledger.md`**. The initial grade writes the Baseline row at close; this routine appends one row per quarter. The ledger is the source of truth — the trend is read from it, never re-derived from memory:

| Quarter | Coverage Score | Grade | Maturity | Gaps Open | Redundancies | Total $ Stake | Top Gap |
|---------|----------------|-------|----------|-----------|--------------|---------------|---------|
| Baseline | | | | | | | |
| Q1 | | | | | | | |
| Q2 | | | | | | | |

A healthy trajectory: score climbing, gap count falling, redundancies falling, total $ stake shrinking, maturity tier moving up (Starter → Foundation → Growth → Enterprise).

## Anti-hallucination

Re-inventory only the tools the buyer re-provides this quarter. If they don't have a fresh stack list, carry forward last quarter's inventory and label it "carried forward, not re-inventoried"; don't fabricate a new stack. The Coverage Score trend is only credible if every point on it traces to a real inventory and a real (or labeled) stage.

## What success looks like

The buyer has a 90-day cadence that shows stack coverage as a line going up and to the right, catches new sprawl the quarter it creeps in (not the year the duplicate contracts auto-renew), catches new must-have gaps the quarter the company scales into them, and re-surfaces the Tech Stack Audit Agent at exactly the moment the data justifies the consolidation engagement.
