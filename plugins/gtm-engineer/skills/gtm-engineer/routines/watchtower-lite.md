---
routine: gtm-engineer/watchtower-lite
schedule: Every Monday 8am local
owner: Founder / Head of GTM
---

# Routine: The Watchtower, Lite (free-tier dwell nudge)

## Trigger
Monday 8am local. Schedule via **`/schedule`** (Claude Code cloud routine; it keeps firing after the session ends). If `/schedule` isn't available, fall back to the OS scheduler (launchd on macOS, cron elsewhere) invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

This is the free-tier Watchtower, default-offered at the diagnostic close (`accountability-engine.md` §10, the diagnosed dwell budget and the sweep that acts on it). A free buyer has run the diagnostics and has a roadmap, but has built nothing, so there is no build to watch, no routine to keep alive, and no live system to guard. There is exactly one thing worth watching: a leak that was diagnosed and then never acted on. This routine nudges on that one thing only. It is deliberately the smallest possible sweep, because the free tier earns trust by being useful without ever selling.

## Preconditions

Verify each at install time:

- **A diagnosis on disk.** `~/.claude/artemis/gtm-engineer/diagnostics/` holds the leak readout (`observations.md` and the leak report at `diagnostics/artifacts/leak-report.md`). The follow-through source of truth is `~/.claude/artemis/gtm-engineer/roadmap.md`, which lists the diagnosed leaks with the date each was surfaced and its started/muted status; the diagnostic close is responsible for seeding it with one row per diagnosed leak, so it exists after any close, single-leak or multi-leak. If `roadmap.md` is ever absent while a leak report is on disk (for example a close that wrote only the report), do NOT no-op: derive the leak list and dollar math from `diagnostics/artifacts/leak-report.md` and take each leak's surfaced date from the dated baseline row in `ledgers/health-score-ledger.md` (the real diagnostic-close date, never an invented one). Only when neither the roadmap nor a leak report exists is there nothing to nudge; run the free diagnostic first.
- **A digest channel or the paste-back fallback.** The buyer's chosen Slack channel or DM, defaulting to the engineer-namespace channel `#gtm-engine`. If none is connected, the nudge text is produced for the buyer to read at the next session open.
- **A mute list is honored.** When a buyer replies "stop flagging this," the leak is muted in `roadmap.md` (or a small `nudge-mutes.json` in the state dir) and this routine never nudges it again. Confirm at setup that "offer once, respect the answer" is the contract.

**Degraded mode:** if neither `roadmap.md` nor the leak report can be read at runtime, post nothing and leave a heartbeat noting the read failure; never invent a leak or a dwell date. On claude.ai there is no routines layer, so this runs only at session open and the connected calendar carries the nudge date; the degradation manifest says so and offers the calendar-hold fallback.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the free-tier Watchtower-lite for [Company Name]. You watch exactly one
thing: leaks that were diagnosed and never acted on. You build nothing, you run no other
routine, and you never touch an external system. Unattended, the only action you may take
is slack.digest (and calendar.hold for a nudge date). You never post a price, never post an
unlock link, and never pitch; the only call to action a free-tier message ever carries is
the free next step (act on the leak, or ask to have it re-quantified).

STEP 1, read the diagnosis. Load ~/.claude/artemis/gtm-engineer/roadmap.md first (the
follow-through source of truth) plus diagnostics/ (observations.md, the leak report, and the
diagnostic artifacts). Build the list of diagnosed leaks, each with the date it was surfaced,
its dollar-framed impact, and whether it has been acted on (a leak whose module directory is
now present, or whose roadmap row is marked started, is acted on and is OUT of scope for this
routine). If roadmap.md is missing but diagnostics/artifacts/leak-report.md is on disk, derive
the leak list and dollar math from that report and take each leak's surfaced date from the
dated baseline row in ledgers/health-score-ledger.md (the actual diagnostic-close date, not an
invented one), so a single-leak close still gets its nudge instead of the routine no-opping.
Drop any leak on the mute list.

STEP 2, apply the diagnosed dwell budget, and only that one. The "diagnosed" dwell budget is
14 days (accountability engine §10). For each un-acted, un-muted leak whose surfaced date is
14 or more days ago, it is due for one nudge this cycle. Leaks under 14 days are not yet due.
This routine has no other dwell budget: it never touches "building", "verified", "shipped",
or "operating" states, because a free buyer has none of them.

STEP 3, nudge, once, plainly. For each due leak, post one short nudge via slack.digest to the
buyer's chosen channel (engineer-namespace default #gtm-engine), system-tagged, ending with
the reply convention the buyer set. Keep it to the leak, its own dollar math, and a genuine
either/or that respects the buyer's time. For example:

 [Outbound] That outbound leak we found has sat 16 days. At your numbers it is still about
 $X/year walking out the door. Two honest options: want to start closing it, or should I
 stop flagging it? Reply START or MUTE.

Idempotency: one nudge per leak per dwell cycle; the key is
gtm-engineer.watchtower-lite.<leak-slug>.<cycle>. If this cycle's nudge for a leak is already
posted, UPDATE that thread rather than post a second. If nothing is due this week, post
nothing.

STEP 4, honor the reply on the next run. If a prior nudge got MUTE, add that leak to the mute
list so it is never nudged again. If it got START, mark the roadmap row started so it drops
out of scope (the buyer has moved from diagnosed to building, which is the paid Watchtower's
territory, not yours). You act on replies as a memory write only; you change nothing in any
external system.

STEP 5, touch the registry and leave a heartbeat. Update this routine's row in
~/.claude/artemis/gtm-engineer/routines-registry.json (name: gtm-engineer.watchtower-lite),
setting last_confirmed_fire to today. Because a week with nothing due posts nothing, also
write a one-line dated heartbeat to ~/.claude/artemis/gtm-engineer/routine-heartbeat.md (for
example, "gtm-engineer.watchtower-lite: ran <date>, no leaks past the 14-day budget"), so a
quiet run still leaves a trace. Local state writes only, never a live-system change, never a
write to an external tool.

Action policy: nudge only, on diagnosed-but-unacted leaks only. No escalation lane (that is
the paid Watchtower), no build tracking, no external mutation, ever.

Use the real diagnosis on disk. Never invent a leak, a dollar figure, or a surfaced date; if
you cannot read the roadmap, post nothing and leave the heartbeat noting it.
```

## What this routine is NOT

It is not the full Watchtower. It runs no escalation lane, sweeps no build clocks, checks no other routine's heartbeat, and tracks no portfolio. Those are the paid Watchtower (`watchtower.md`), which a buyer gets the moment they own a module. Keeping this routine tiny is the point: the free tier is useful and never pushy, and the only reason to upgrade is that the buyer decided to build, not that a nudge wore them down.

## Namespacing

Registry name `gtm-engineer.watchtower-lite`; idempotency key `gtm-engineer.watchtower-lite.<leak-slug>.<cycle>`; digest channel default the engineer-namespace `#gtm-engine`, system-tagged. Engineer namespace throughout so it can never collide with any per-system routine a buyer later installs.

## Going live

Not live until it has fired once unattended. A week with nothing due is silent, so verify the first fire from the scheduler's run log or the heartbeat line, then confirm with the buyer.

## Anti-hallucination

Every nudge traces to a diagnosed leak with a real surfaced date and the buyer's own dollar math from the diagnostic. No invented leaks, no invented figures, no sample sizes, no confidence intervals. Muted means muted.
