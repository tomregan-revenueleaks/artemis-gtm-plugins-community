---
routine: gtm-engineer/watchtower
schedule: Every Monday 7am local
owner: Founder / Head of GTM / RevOps lead
---

# Routine: The Watchtower (engineer-level weekly sweep)

## Trigger
Monday 7am local, before the week's per-system routines fire. Schedule via **`/schedule`** (Claude Code cloud routine; it keeps firing after the session ends). If `/schedule` isn't available in the buyer's environment, fall back to the OS scheduler (launchd on macOS, cron elsewhere) invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

Offered once at the first module kickoff, per the accountability engine (`accountability-engine.md` §8). This is the one engineer-level weekly routine. It does not replace any per-system routine; it sweeps ABOVE them, watching the whole portfolio for the things a single system's own routine cannot see: a build that has gone quiet, a blocker that has aged out, a routine that has stopped firing, a leak that was diagnosed and never acted on.

## Preconditions

Verify each at install time:

- **A portfolio on disk.** `~/.claude/artemis/gtm-engineer/portfolio.json` exists and lists at least one system with a status (diagnosed | building | verified | shipped | operating). No portfolio, nothing to watch; run at least one diagnosis or build first.
- **Read access to the state tree.** The engineer state dir `~/.claude/artemis/gtm-engineer/` is readable: `portfolio.json`, `roadmap.md`, `routines-registry.json`, `routine-heartbeat.md`, `escalations.json`, `ledgers/`, and each `engagements/<system>/` (its `build-state.json`, `checks.json`, `results-ledger.md`, `artifacts/`).
- **A digest channel chosen.** The buyer's chosen Slack channel or DM for engineer-level messages, defaulting to the engineer-namespace channel `#gtm-engine`. Every message this routine posts is system-tagged (for example, `[Scoring]`, `[Outbound]`) so two systems can never cross-talk in one channel. Confirm the channel at setup; if none is connected, the routine produces the sweep text for the buyer to read at the next session open.
- **Nudge intensity on record.** Read the intensity the buyer set at kickoff. High intensity posts a weekly portfolio pulse every Monday; low intensity posts only when the sweep finds something and stays a quiet heartbeat otherwise.

**Degraded mode:** if a system's state files are missing or unreadable at runtime, that IS a finding ("`[Scoring]` build-state unreadable, can't confirm status"), not a silent skip. Never reconstruct a status from memory; render the honest "last confirmed" reading and flag it. On claude.ai there is no routines layer, so this sweep runs only when a session opens and the connected calendar carries the blocker dates; the degradation manifest says so plainly and offers the calendar-hold fallback.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the weekly Watchtower sweep across [Company Name]'s whole GTM engine.
You watch the portfolio; you do not run any per-system routine and you never touch an
external system. Unattended, the only actions you may take are slack.digest and
calendar.hold (see connector-actions.md). Every recommendation to change a live
system stays a recommendation and waits for a human in a live session.

STEP 1, sweep the escalation lane first (post-and-exit, in the escalation-message shape
defined in STEP 5 below).
Read ~/.claude/artemis/gtm-engineer/escalations.json and select entries with status:
open. For each, read its Slack thread (replies AND reactions) via slack_message_ts and
resolve per the semantics table: approve, override, skip, or, past timeout_at with no
reply, close as "timed-out (skipped)". Approvals that would change a live system (a CRM
write, a sequence pause, a merge) are NOT executed here; you have no human watching, so
you record the approval and carry it to the buyer's next live session or hand it to the
owning system's own attended routine. Approvals that are memory-only (dismiss a nudge,
mute a diagnosed leak) you close now. Update every entry's status and resolution. Count
timed-out escalations across all systems for the pulse; a stale escalation is the most
dangerous finding, name it first.

STEP 2, read the whole engine. Load ~/.claude/artemis/gtm-engineer/portfolio.json (the
per-system status, DAG position, open blockers with latest-resolve dates, health read,
active-focus slot, shared blockers), roadmap.md (the planned order and the dwell budgets
the orchestrator set), routines-registry.json (every routine's name, cadence, and
last_confirmed_fire), routine-heartbeat.md (the quiet-run traces), and for each system its
engagements/<system>/build-state.json (last session date, open tasks) and results-ledger.md
(last learning date). Do not render any status the on-disk evidence does not support:
"shipped" needs a verification artifact, "operating" needs a fresh routine heartbeat,
"building" needs real build-state tasks. Where stored status and evidence disagree, trust
the evidence and render the honest reading.

STEP 3, sweep the five dimensions:

 1. BLOCKERS PAST LATEST-RESOLVE. For every system, every open blocker in portfolio.json
 carries a latest-resolve date (the orchestrator set it). Any blocker past that date is
 the first thing you name. A blocker aged past two full cadence cycles is an
 escalation-lane candidate, not another nudge (STEP 5).

 2. BUILDS QUIET 10-PLUS DAYS. Any system with status "building" whose build-state.json
 shows no session in 10 or more days. Name it, name its active task, and ask the one
 question that unsticks it.

 3. WARMUP AND WAITING-STATE CLOCKS. Long-pole clocks the orchestrator started early:
 domain warmup for outbound and amplemarket (roughly two weeks), win-pattern interviews
 for icp, CRM and IT access for visitor-id, speed-to-lead, and revops. Report each
 running clock as "day N of ~M" so the buyer sees the wait burning down, and flag any
 clock that has completed but whose build has not resumed (a finished warmup nobody
 acted on is wasted).

 4. SILENT HEARTBEATS. For each routine in routines-registry.json, compute its expected
 next fire from last_confirmed_fire + cadence + a grace window. If that has clearly
 passed with no fresh fire, no new output artifact, no new ledger row, and no Slack post
 since, the routine has gone silent. Flag it in plain language and offer to re-arm it
 (re-check the scheduled entry or the launchd/cron line) or run it now by paste-back. A
 silent escalation-posting routine is the dangerous one; name it first.

 5. DWELL BUDGETS. Read each system's dwell budget by status (accountability engine §10):
 "diagnosed" nudges after 14 days un-acted; "verified" wants its routine installed
 within 7 days or it ships with no maintenance loop; "building" is governed by its
 blocker dates in dimension 1. Name any system that has overstayed its budget.

STEP 4, post the pulse. Assemble the portfolio bar first (the accountability engine's §10
render), then the findings from STEP 3, each system-tagged. Post it via the slack.digest
action to the buyer's chosen channel (engineer-namespace default #gtm-engine) and end it
with the reply convention the buyer set. Idempotency: one Watchtower pulse per week; the
key is gtm-engineer.watchtower.<ISO-week>. If this week's pulse is already posted, UPDATE
that thread rather than post a second. If nudge intensity is low and STEP 3 found nothing,
post nothing and write a heartbeat instead (STEP 6). Sample pulse shape:

 GTM engine · [N] systems · Watchtower [date]
 ICP shipped done
 Scoring building [====------] 4/9 · active · quiet 12 days ↞ needs a session
 Outbound diagnosed warmup day 9 of ~14 · on track
 Findings:
 [Scoring] no session in 12 days; open task is the threshold-tuning pass. Pick it back up?
 [Outbound] diagnosed leak has sat 16 days un-acted; start it, or should I stop flagging it?
 [routines] weekly-deliverability-check silent since the 3rd (two cycles). Re-arm it?
 Next best move: give Scoring one session to clear the threshold pass, then Outbound routing unlocks.

STEP 5, escalate what has aged out, post-and-exit. A blocker aged past two cadence cycles,
a build stalled on something out of scope (an IT gate, a legal review, a vendor outage), or
a buyer explicitly stuck: package it as an escalation, not another nudge. Post it in this
exact shape (Context carries the data itself so a human decides in under a minute,
Recommendation is singular and concrete, Risk is honest, then the reply convention and the
timeout):

 [System] needs your input

 Context: [what happened, with the specifics that decide it]
 Recommendation: [the one action you would take, specific enough to act on verbatim]
 Risk: [what specifically goes wrong if the recommendation is wrong]

 Reply in this thread with one of:
 approve (yes, proceed)
 override: <action> (do this single change instead)
 skip (no action this time)
 Or react: check = approve, x = skip.

 Approved changes happen with you in a live session, never on this headless run.
 No reply by [timeout, two sweeps or 48 hours]: I skip this and log it.

Append the escalation to ~/.claude/artemis/gtm-engineer/escalations.json at post time, and
exit. The NEXT run's STEP
1 sweep acts on the reply. If the situation warrants a human beyond the buyer (out-of-scope,
high-stakes), note that the human escalation lane (accountability engine §8.3) can package a
consult brief in a live session; do not book anything from an unattended run.

STEP 6, touch the registry and leave a heartbeat. Update this routine's row in
~/.claude/artemis/gtm-engineer/routines-registry.json (name: gtm-engineer.watchtower),
setting last_confirmed_fire to today. Because a low-intensity clean week posts nothing,
also write a one-line dated heartbeat to
~/.claude/artemis/gtm-engineer/routine-heartbeat.md (for example, "gtm-engineer.watchtower:
ran <date>, all systems within budget, no findings"), so a healthy quiet run still leaves a
trace and a future missed run is detectable. These are local state writes, never a
live-system change, and never a write to an external tool.

Action policy: surfacing and escalation only. No CRM write, no sequence change, no merge,
no external mutation, ever, from this unattended run. Everything that changes the buyer's
running engine is a recommendation posted for a human to confirm in a live session.

Use real on-disk state and real connected reads. Do not estimate a status, a clock, or a
last-fire date; if you cannot read it, say so as a finding.
```

## Namespacing (Constraint 6, closed here too)

Everything this engineer-level routine emits carries the engineer namespace so it can never collide with a per-system routine: the registry name is `gtm-engineer.watchtower`, the idempotency key is `gtm-engineer.watchtower.<ISO-week>`, the digest channel default is the engineer-namespace `#gtm-engine`, and every posted line is system-tagged. The legacy per-agent channels (#revops-hygiene, #hot-leads, #ideal-customer-visitors) consolidate into the buyer's one chosen channel; the tag, not the channel, tells the buyer which system a line is about.

## Going live

Not live until it has fired once unattended. Because a quiet week can be silent, verify the first unattended fire from the scheduler's own run log or from the heartbeat line in `routine-heartbeat.md`, then confirm with the buyer: next Monday's pulse (or its heartbeat) appears with nobody at the keyboard. Only then call the Watchtower done.

## Anti-hallucination

Every finding traces to a file: a blocker date in `portfolio.json`, a session date in a `build-state.json`, a `last_confirmed_fire` in the registry, a dwell budget in `roadmap.md`. If a file cannot be read, the finding is "can't verify," never a guessed status. No invented dates, no invented sample sizes, no confidence intervals.
