---
routine: gtm-engineer/portfolio-review
schedule: First of each month
owner: Founder / Head of GTM / RevOps lead
---

# Routine: Monthly Portfolio Review (cross-system learning)

## Trigger
First of each month. Schedule via **`/schedule`** (Claude Code cloud routine; it keeps firing after the session ends). If `/schedule` isn't available, fall back to the OS scheduler (launchd on macOS, cron elsewhere) invoking `claude -p "<the prompt below>"` headless. Never wire this to a session-bound mechanism; if it dies when the session closes, it was never a routine.

This is the cross-system learning loop (`results-memory.md`, plan §9). Each system's own routine learns from that system's results. This routine reads EVERY ledger at once and routes what one system learned into the systems that should act on it: outbound's reply-rate-by-segment sharpens the ICP refresh, visitor-id's winning industries inform the scoring weights, content's best-converting pillar tells nurture what to lead with. One product, one place where the whole engine's learning compounds instead of nineteen ledgers that never talk to each other.

The hard rule, stated once and binding: **ledger appends are the only automatic writes this routine makes.** Every change to a live system (a scoring weight, an ICP filter, a sequence, a pillar) stays a recommendation surfaced for a human. Memory is automatic; changes are not. That line does not move.

## Preconditions

Verify each at install time:

- **At least two systems with results ledgers.** Cross-system learning needs more than one source. Read `~/.claude/artemis/gtm-engineer/engagements/<system>/results-ledger.md` for each shipped or operating system; the routine is worth scheduling once two or more exist. With one, its own per-system routine already covers it.
- **The engineer ledgers are readable.** `~/.claude/artemis/gtm-engineer/ledgers/portfolio-ledger.md` (the cross-system learning ledger this routine appends to), `roi-ledger.json`, and `health-score-ledger.md`. The buyer profile `~/.claude/artemis/_profile/profile.json` is readable so copy learnings can update its `voice` block.
- **A digest channel chosen.** The buyer's chosen channel, defaulting to the engineer-namespace `#gtm-engine`. If none is connected, the review is dropped in the state dir for the buyer.

**Degraded mode:** if a system's results ledger is unreadable at runtime, review the readable ones and name the missing system in the report header; never reconstruct a learning from memory. If only one ledger has entries this month, say so plainly and route nothing rather than manufacture a cross-system link that the data does not support.

## Prompt to Claude (give this to `/schedule`, or to the OS-scheduler fallback)

```
You are running the monthly Portfolio Review for [Company Name]. You read every system's
results ledger and route learnings across the engine. You change nothing in any live
system: your only automatic writes are ledger appends and, for copy learnings, the buyer
profile voice block. Every operational change you find is a RECOMMENDATION posted for a
human, never an action.

STEP 1, read every ledger. Load each
~/.claude/artemis/gtm-engineer/engagements/<system>/results-ledger.md (the per-system
learnings), ~/.claude/artemis/gtm-engineer/ledgers/portfolio-ledger.md (the cross-system
learnings you have routed before, so you do not repeat one), roi-ledger.json (the structured
predicted-vs-measured rows), and health-score-ledger.md (the trend). Diff this month against
the accumulating history, not just the frozen build-time targets.

STEP 2, find the cross-system signals. Look for a learning in one system's ledger that
another system should act on. The canonical routes (extend them as the buyer's ledgers
justify, never invent one the data does not support):
 - Outbound reply-rate-by-segment -> ICP refresh (which segments actually convert
 reshape the fit model and the win-pattern weights).
 - Visitor-id winning industries -> Scoring weights (industries that show up and close
 should carry more fit weight).
 - Content best-converting pillar -> Nurture lead message and Conversion Content angle.
 - Speed-to-lead response-band wins -> Qualification routing thresholds.
 - Scoring threshold that separated wins from losses -> Qualification's SQL bar.
For each candidate route, require that BOTH sides exist: a real learning in the source
ledger and a live consuming system that can use it. A learning with no consumer yet is noted
for later, not forced.

STEP 3, write the review. Post a cross-system learning digest via slack.digest to the
buyer's chosen channel (engineer-namespace default #gtm-engine), system-tagged, ending with
the reply convention the buyer set. Idempotency: one review per month; the key is
gtm-engineer.portfolio-review.<YYYY-MM>. If this month's is already posted, UPDATE that
thread rather than post a second. Shape:
 - The 2-3 highest-value routed learnings, each as: what [source system] learned, which
 [target system] should act on it, and the single concrete change you RECOMMEND (stated
 as a recommendation the buyer confirms in a live session, with its own reasoning).
 - What held and what decayed across the portfolio this month (promote winners, name any
 dimension or tactic that has failed to earn its place two cycles running and should be
 down-weighted).
 - Nothing to route this month is a valid, honest output; say so rather than stretch.

STEP 4, append the automatic writes, and only these. Append one dated row per routed
learning to ~/.claude/artemis/gtm-engineer/ledgers/portfolio-ledger.md: the date, the source
system and its learning, the target system, and the one-sentence advisor judgment (the human
line, no raw-data dump, no invented sample sizes, no confidence intervals). When a routed
learning is about COPY (a subject line, message, or framing that won), also update the voice
block in ~/.claude/artemis/_profile/profile.json so the next copy-writing system inherits it
(per results-memory.md, "Voice learnings compound into the buyer profile"). These two are
memory writes. You append nothing to any live system and you flip no scoring weight, filter,
sequence, or gate; those wait for the buyer to confirm the recommendation in a live session.

STEP 5, touch the registry. Update this routine's row in
~/.claude/artemis/gtm-engineer/routines-registry.json (name:
gtm-engineer.portfolio-review), setting last_confirmed_fire to today. This routine posts a
review and appends ledger rows on every run, so those are the dated trace; no separate
heartbeat is needed. Local state write, never a live-system change.

Action policy: read, route, recommend, and append to memory. No external mutation, ever.
Ledger appends and the profile voice block are the ONLY automatic writes; every live change
is a recommendation a human confirms.

Use real ledger data. Never invent a learning, a segment, or a number; if a ledger is
missing, name it and route only what the readable ledgers support.
```

## The cross-system ledger (the durable record of routed learning)

The routed learnings live in one named file: **`~/.claude/artemis/gtm-engineer/ledgers/portfolio-ledger.md`**. It is distinct from the per-system results ledgers (which never merge with it, per canon) and from the ROI ledger (which is structured numbers). This file is the human-readable trail of how one system's outcome sharpened another's build, one dated line per routed learning, so a year in, the buyer can see the engine teaching itself.

## Namespacing

Registry name `gtm-engineer.portfolio-review`; idempotency key `gtm-engineer.portfolio-review.<YYYY-MM>`; digest channel default the engineer-namespace `#gtm-engine`, system-tagged. Engineer namespace throughout.

## Going live

Not live until it has fired once unattended. The first run may find little to route with only a month of history; the pass bar is a correctly shaped review that routes what the ledgers actually support (which can be nothing). Confirm the first unattended review with the buyer before calling it done.

## Anti-hallucination

Every routed learning traces to a real row in a real system's results ledger. No invented cross-system link, no invented segment or industry, no invented number, no sample sizes, no confidence intervals. When only one system has data, the honest output is "nothing to route yet."
