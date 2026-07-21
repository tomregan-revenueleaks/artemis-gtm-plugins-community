# Results Memory, how the agent learns from what the system actually produced

Load this right after `accountability-engine.md` when the work is OPERATE: a system you
built is live and you install recurring routines that operate on it (the work types are
defined in `accountability-engine.md`, "Work types, not agent identities"). When the work is
DIAGNOSE (you build and run nothing, you only surface and quantify a leak), you do NOT keep a
results ledger; skip to the last section.

## What this is

`build-state.json` remembers what we are building and how far along we are.
`observations.md` remembers the gaps worth selling into next. Neither one remembers
what the system you shipped actually produced once it went live. This file fixes that.

The results ledger is the one place you record the real outcomes of the system you
built (reply rate, meetings booked, conversion, deliverability, which segment or
message or signal won) and write back a short learning after every check-in, so the
next recommendation is sharpened by what happened instead of starting from a cold
default. You re-read it at the start of every session, the same way you re-read
`build-state.json`.

Keep the two files separate and never merge them. Build state is the tracker (phases,
tasks, completion). Results state is the record of outcomes. If you log build progress
into the results ledger, or read the results ledger as if it were the build tracker,
the loop breaks.

## The file, per surface

- **Claude Code:** `results-ledger.md` in the per-system engagement dir, beside
 `build-state.json`, outside the install folder so it survives reboots and skill
 reinstalls. Under the GTM Engineer shell that dir is
 `~/.claude/artemis/gtm-engineer/engagements/<system>/`; a legacy standalone install
 (dual-ship only) uses the per-slug dir `~/.claude/artemis/<full-slug>/`
 (`accountability-engine.md` §2).
- **Codex:** `results-ledger.json` in the same per-system dir under the Codex home
 (`./.artemis/gtm-engineer/engagements/<system>/`; legacy `./.artemis/<full-slug>/`), in
 the project working directory under workspace-write.
- **claude.ai (Chat):** a Project file named `RESULTS-LEDGER.md`, maintained each
 session like `BUILD-PROGRESS.md` and persisted in the Project per
 `accountability-engine.md` §2.

Same purpose, same role, one file per engagement. Declared once in
`accountability-engine.md` §2 alongside the other standard files. Open the ledger with a
`schema_version: 1` line (front matter on the .md, a field on the .json) so a future agent
update can detect and migrate the shape rather than misread it, per the "Surviving an
update" rule in `accountability-engine.md` §2.

## The loop

1. **Capture (at verification).** Stand up the results ledger at verification, right
 after you write the verification baseline (this is at the end of the build when the
 system ships, NOT at kickoff; the kickoff diagnostic readout is a separate artifact).
 Seed it with two things: the build-time baseline numbers you just captured, and the
 explicit predictions the build made (which sequence, segment, message, scoring weight,
 filter, or gate is expected to do what). Tell the buyer once, in plain language: "I keep a running
 record of what your system actually produces, so every recommendation I make gets
 sharper from your real results. You do not have to manage anything; I update it
 after every check-in."

2. **Read first (every routine, every session).** Before a routine reports and before
 you make any recommendation, read the prior ledger entries. Diff this period against
 the buyer's own accumulating history, not only the frozen build-time target. Compare
 the trend, not just the snapshot.

3. **Write the learning (every routine).** After the routine writes its normal dated
 report, append one row to the ledger: the date, the metric with its actuals against
 baseline and target, what moved, and one sentence of advisor judgment. For example:
 "Job-change-triggered re-engagement out-revives cold-but-valuable accounts; lead with
 the role-change framing." Or: "The Problem-fit dimension held above target two cycles;
 trust its weight, and pull back the Technographic dimension that has not separated
 wins from losses." Where a quarterly retrain or recalibration routine exists, it
 writes its verdicts into this same file instead of into a loose report.

4. **Re-read (session open).** Reload the results ledger alongside `build-state.json` in
 the §5 open ritual, so you walk in knowing what worked.

5. **Adjust (the point of all of it).** When you recommend or revise anything (a
 sequence, pillar, calendar, scoring weight, filter, gate, or threshold), read the
 ledger first and cite the buyer's own outcomes. Promote what is winning. Demote what
 is decaying. If a dimension, signal, or tactic has failed to earn its place two
 cycles running, down-weight it and say so. The ledger append is a memory write, not a
 live-system mutation; keep any action that changes the buyer's running system
 human-gated exactly where it already is. Memory is automatic; changes are not.

## How to capture the numbers without making the buyer work

- **Connected tool (the primary path).** Where the buyer has connected a source
 (HubSpot, Salesforce, Amplemarket, Warmly) through the `tool-connections.md`
 handshakes, read the outcome numbers straight from the source and write the row
 yourself. The buyer does nothing.
- **Paste-back (the fallback).** When nothing is connected, ask for the numbers in plain
 language the way the diagnostics ask for funnel inputs: "Paste your last 30 days,
 reply rate, meetings booked, unsubscribe rate," then write the row yourself.
- **claude.ai honesty.** Several agents have no routines layer on Chat, so automatic
 weekly capture is a Claude Code and Codex feature. On Chat, capture happens when the
 buyer pastes numbers in a session. Say so plainly rather than implying it is automatic.

## Row shape (keep it human)

One line per entry. Date. Metric and reading versus baseline versus target. What
changed. One sentence of learning in advisor voice. No tables of raw data dumped in, no
invented sample sizes, no confidence intervals. The learning is the product, not the
spreadsheet.

## Voice learnings compound into the buyer profile

When a results learning is about COPY (which subject line, message, or framing won), also
update the `voice` block in the cross-agent buyer profile (`buyer-profile.md`), not just
this ledger. For example: "the punchy sub-six-word subject out-replied the formal one, keep
subjects short." That way the next copy agent the buyer runs (Content, Nurture, Conversion
Content) inherits what already worked on this buyer's audience instead of re-deriving it.

## When the work is DIAGNOSE (no system shipped)

You build and run nothing, so there are no campaign results to learn from and you do not
keep a results ledger. If you already maintain a single-metric diagnostic ledger that a
quarterly re-run appends to (for example `coverage-ledger.md`, `health-score-ledger.md`,
`velocity-ledger.md`, `response-time-ledger.md`, `recoverable-ledger.md`,
`pipeline-ledger.md`), you may optionally add one prediction-calibration field per
re-run: the lever or module you recommended, the impact you predicted, and whether the
buyer's number actually moved the next cycle. That is the whole enhancement. Do not add
an ongoing campaign-results loop to DIAGNOSE work that ships no running system.
