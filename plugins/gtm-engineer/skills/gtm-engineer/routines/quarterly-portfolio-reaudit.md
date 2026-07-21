---
routine: gtm-engineer/quarterly-portfolio-reaudit
schedule: First Monday of each calendar quarter, 9am local
owner: Founder / Head of GTM / RevOps lead
---

# Routine: Quarterly Portfolio Re-Audit (the scale advisor)

## Trigger
First Monday of each calendar quarter, 9am local. Schedule via **`/schedule`** (Claude Code cloud routine; it keeps firing after the session ends). If `/schedule` isn't available, fall back to the OS scheduler (launchd on macOS, cron elsewhere) invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

This is the engineer-level post-build scale advisor (plan §10). It sits ABOVE the single-system quarterly re-audit (`diagnose/routines/quarterly-reaudit.md`, which tracks one system's health-score trend). This routine re-measures the whole engine every 90 days: the portfolio health score, every open prediction across every system, drift in the systems that shipped, and fresh leak detection on the systems the buyer has not built yet. It composes the per-system re-audit where one exists rather than duplicating it, and rolls the results up into one portfolio view with a named next phase.

Where the single-system re-audit answers "did this one system's leaks stay fixed," this one answers "where does the whole engine stand, which of our calls held, and what is the next phase." The next-phase recommendation arrives in the buyer's own leak math and DAG position, never as a pitch.

## Preconditions

Verify each at install time:

- **A portfolio with history.** `~/.claude/artemis/gtm-engineer/portfolio.json` lists the systems and their stages, and `~/.claude/artemis/gtm-engineer/ledgers/health-score-ledger.md` holds at least a Baseline row. No baseline, no trend; run the initial diagnosis first.
- **Ledgers and checks readable.** `ledgers/health-score-ledger.md`, `ledgers/roi-ledger.json`, `ledgers/portfolio-ledger.md`, each `engagements/<system>/results-ledger.md`, each `engagements/<system>/checks.json` (the VERIFY structured checks, for the drift pass), and `roadmap.md`. `system-graph.md` (the DAG) is available for DAG position and next-phase ordering.
- **A path to fresh numbers.** A CRM or engagement tool connected to Claude (per `tool-connections.md`) for read-only re-measurement, or the buyer's commitment to paste this quarter's metrics when the routine asks. Note which per system in `portfolio.json`.
- **A delivery channel (optional).** The buyer's chosen channel, defaulting to the engineer-namespace `#gtm-engine`; otherwise the report lands in the artifacts dir.

**Degraded mode:** if fresh numbers are unavailable for a system when the routine fires, carry forward last quarter's values labeled "carried forward, not re-measured," write the report anyway, and flag which metrics need real numbers next session. If `checks.json` is unreadable for a shipped system, report the drift pass for that system as "could not verify, checks unreadable" rather than assuming it is healthy. Never fabricate a reading. On claude.ai there is no routines layer; the re-audit runs at session open and the report lands in a Project file.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the quarterly Portfolio Re-Audit for [Company Name] across the whole GTM
engine. You re-measure, you grade, you check for drift, and you recommend the next phase.
You change nothing in any live system: read-only re-measurement, ledger appends, and the
report are your only writes. Every build or fix you recommend is a recommendation a human
confirms.

STEP 1, read the prior state and the open predictions. Load portfolio.json, roadmap.md,
health-score-ledger.md, roi-ledger.json, portfolio-ledger.md, and each system's
results-ledger.md and checks.json. Off the last health-score-ledger row and the open
roi-ledger rows, read every prediction still open: which fix, which system, and the
projected $/yr recovery attached to it. Hold them; those are the calls you are about to
grade.

STEP 2, re-measure the portfolio health score. For each system, pull the same inputs the
original diagnosis used (connected read per tool-connections.md where wired, otherwise
paste-back), re-benchmark each metric against the industry-by-motion table (respect the
motion guardrail: PLG stays on PLG metrics, SLG on sales metrics), and recompute the overall
0-100 health score and signal tier for the engine. Where a per-system quarterly re-audit
already ran this quarter and wrote its own trend, READ its result rather than re-running it;
compose, do not duplicate.

STEP 3, grade every open prediction. For each prediction from STEP 1: did the buyer act on
it (is the module built, the fix shipped)? Did the metric actually move? Read projected
versus measured recovery from roi-ledger.json where the row exists. Write one human line of
advisor judgment per prediction, the way the results-memory note frames it: the call held,
the call missed and why (recalibrate), or they did not act so the projection stands and
carries forward. One line each, no sample sizes, no confidence intervals.

STEP 4, drift check on shipped systems. For each system at status shipped or operating,
re-run its VERIFY checks from engagements/<system>/checks.json against the current
configuration (read-only) and diff against its results-ledger.md. Flag config rot: a routing
rule that stopped firing, a score that stopped writing, a warmup that lapsed, a deliverability
band that slipped. Drift is a finding and a recommendation to re-verify in a live session,
never an auto-fix.

STEP 5, re-run leak detection on the un-built systems. For every system still at status
diagnosed (or never diagnosed) that the DAG says the buyer's stage now warrants, re-quantify
its leak at this quarter's numbers (the dollar figure moves as volume and deal size change).
Watch for growth-induced new leaks: more traffic exposing a slow-response gap, a new motion
exposing an outbound gap, a team crossing the qualification floor. Also evaluate the
motion-stage triggers (plan §10.2): lead volume doubling, team shape crossing a floor, new
segment entry, ARR band crossing a maturity boundary.

STEP 6, write the portfolio trend report and the next phase. Assemble:
 - PORTFOLIO HEALTH TREND: this quarter's engine score vs last quarter vs baseline, the
 trajectory, and any DROP named loudly as a regression.
 - PREDICTIONS GRADED: STEP 3's one-liners.
 - DRIFT: STEP 4's findings, per shipped system, each with a re-verify recommendation.
 - OPEN AND NEW LEAKS: STEP 5's re-quantified leaks, each mapped to its module and DAG
 position.
 - THE NEXT PHASE: rank open and new leaks by current dollar impact, ordered by the DAG
 (prerequisites first even when a downstream leak is bigger). Present the top one as "our
 next phase," named, with its DAG position shown and its projected recovery computed from
 the buyer's own ledger where data exists. Savings framing (a bundle) only at three or
 more open leaks, and quote any price by reading module-manifest.json at that moment,
 never from prose or memory.
Post the summary via slack.digest to the buyer's chosen channel (engineer-namespace default
#gtm-engine), system-tagged, leading with the engine score trajectory. Idempotency: one
re-audit per quarter; the key is gtm-engineer.quarterly-portfolio-reaudit.<YYYY-Qn>. If this
quarter's is already posted, UPDATE that thread rather than post a second.

STEP 7, write to disk. Append this quarter's portfolio row to
~/.claude/artemis/gtm-engineer/ledgers/health-score-ledger.md (the engine score, signal
tier, open-leak count, total dollar impact, this quarter's calibration read on last
quarter's prediction, and the new next-phase recommendation with its projected $/yr
recovery). Save the full trend report to
~/.claude/artemis/gtm-engineer/diagnostics/artifacts/portfolio-reaudit-[YYYY-Qn].md. These
are memory writes. Any change to a live system (build the next phase, re-verify a drifted
config) stays a recommendation the buyer confirms in a live session.

STEP 8, touch the registry. Update this routine's row in
~/.claude/artemis/gtm-engineer/routines-registry.json (name:
gtm-engineer.quarterly-portfolio-reaudit), setting last_confirmed_fire to today. It writes a
report and appends a ledger row on every run, so those are the dated trace; no separate
heartbeat is needed. Local state write, never a live-system change.

Action policy: re-measure (read-only), grade, and recommend. No CRM write, no config change,
no external mutation, ever, from this unattended run. The next phase is a recommendation, not
an action.

Use real re-measured numbers or named benchmark substitutions. Never invent a metric the
buyer did not provide this quarter; carry forward and label rather than fabricate. No sample
sizes, no confidence intervals.
```

## Relationship to the single-system re-audit

`diagnose/routines/quarterly-reaudit.md` tracks one system's health-score trend and writes to the same `health-score-ledger.md`. This portfolio re-audit reads that ledger, so where the single-system routine has already run for a system this quarter, this one composes its result instead of re-measuring. Where a system has no per-system re-audit (most shipped modules rely on this portfolio pass), this routine is its quarterly re-measurement. They share the ledger and never double-write the same system's row in the same quarter.

## Namespacing

Registry name `gtm-engineer.quarterly-portfolio-reaudit`; idempotency key `gtm-engineer.quarterly-portfolio-reaudit.<YYYY-Qn>`; digest channel default the engineer-namespace `#gtm-engine`, system-tagged. Engineer namespace throughout.

## Going live

Not live until it has fired once unattended. The first run lands with limited history and the pass bar is a correctly shaped trend report on whatever exists. Confirm the first unattended report with the buyer before calling the scale-advisor layer done.

## Anti-hallucination

Re-benchmark only metrics the buyer re-provides or a connected tool returns this quarter; carry forward and label the rest. Drift findings trace to `checks.json`, predictions to the ledger rows, leaks to the buyer's current numbers. No invented readings, no sample sizes, no confidence intervals. A portfolio trend is only credible if every point on it traces to real inputs.
