# Instrumentation Baseline, the measurement substrate under every build

The data-infrastructure half of the self-improvement pillar. `results-memory.md` learns
from what a shipped system produced; this file guarantees the system was built so those
numbers actually exist and mean one thing. Without it, the results ledger fills with
paste-back guesses and definitions that drift from module to module. With it, every shipped
system's loop inputs are instrumented at build time, not hoped for afterward.

Phase-loaded during BUILD. The fields it prescribes are carried by the CRM field registry
where your install includes a build module (`adapters/crm/<crm>/field-registry.md`), so the
fields it prescribes are the fields the build actually writes; otherwise apply these column
definitions directly. It pairs with `output-integrity.md` (the consistency pass reads the
KPI definitions from here) and `results-memory.md` (the baseline captured here seeds the
ledger).

---

## Why this exists

The pillar promise is that the Engineer gets sharper from the buyer's real results. That is
only true if three things are laid down while the system is being built:

1. the outcome can be attributed back to the system that produced it,
2. the traffic and campaigns carry consistent tags so the joins work, and
3. every metric is defined once so "reply rate" or "MQL-to-SQL" means the same thing in every
 module and every session.

Retrofitting these after a system ships is expensive and lossy. Instrument during the build,
as part of the build, never as a later clean-up.

## 1. Attribution field schema

Every shipped system needs a small set of CRM fields so its outcomes trace back to it. These
are designed once, as one coordinated data model, and written physically by the field
registry, so six modules do not each invent their own uncoordinated attribution fields (the
live defect the registry fixes).

The baseline attribution set, per record where applicable:

- **Source system.** Which built system created or touched this record (visitor-id, outbound,
 content, nurture, and so on). The single most important field: without it, nothing
 attributes.
- **First-touch and last-touch.** The channel and campaign that opened the relationship and
 the one that moved it to the current stage.
- **Campaign / sequence identifier.** The specific outbound sequence, nurture track, or
 content pillar responsible, keyed so it joins to the campaign record.
- **Score at conversion.** The fit+intent score at the moment the record crossed each stage
 boundary, so scoring's contribution is measurable rather than assumed.
- **Stage-transition timestamps.** When the record entered MQL, SQL, opportunity, so cycle
 time and stage conversion are computable, not estimated.

The registry owns the exact field names, types, and per-CRM property specs. This file owns
the requirement: no system ships without its attribution fields present and writing.

## 2. UTM discipline

Attribution across the capture layer depends on a locked UTM vocabulary. Free-text UTMs are
the reason most content and outbound attribution is unrecoverable. The build establishes one
taxonomy and enforces it:

- **utm_source:** the platform (the named tool or channel), from a closed list, never freeform.
- **utm_medium:** the motion (organic, outbound, nurture, paid, referral), from a closed list.
- **utm_campaign:** the campaign, in a fixed naming convention (motion-quarter-theme) so
 campaigns sort and group without manual clean-up.
- **utm_content and utm_term:** the variant and the keyword or signal, when applicable.

Lock the vocabulary in the buyer's own reference (a shared doc or the CRM's campaign object)
and hand it to them as an artifact. Every link the built systems emit carries a UTM from this
taxonomy. A link with a freeform UTM is a broken join waiting to happen; name that when you
see one in the buyer's current setup.

## 3. Definition-locked KPI dictionary

Each KPI the built systems will report is defined exactly once, so the number is the same
metric everywhere it appears. A definition is not locked until it names four things:

- **Numerator and denominator.** "MQL-to-SQL" is SQLs accepted over MQLs created in the same
 cohort, not SQLs this month over MQLs this month (a cohort mismatch that inflates or deflates
 the rate).
- **Window.** The time window the rate is measured over, and whether it is cohorted or
 point-in-time.
- **Unit.** Accounts or contacts, annual or monthly, dollars or count. Unit drift is the most
 common silent error.
- **Provenance class.** Whether this KPI is measured (read from a connected tool, with the
 record count stated), reported (buyer paste-back), or benchmark (a labeled substitution,
 never itself treated as a result).

Write the dictionary as an artifact the buyer keeps. When two modules both report a shared
metric (scoring and qualification both touch conversion rate), they cite the same locked
definition, so their numbers reconcile. The output-integrity consistency pass reads these
definitions when it checks that an artifact agrees with itself.

## 4. The baseline capture rule

At verification, when the system ships, capture the baseline reading of every KPI the system
is expected to move, each with its provenance class, and seed `results-ledger.md` with it
alongside the build's explicit predictions (`results-memory.md`, loop step 1). The baseline is
the line every later routine diffs against. A results loop with no baseline can report a number
but cannot say whether it improved; the baseline is what makes the loop mean something.

Capture the baseline the same way the ledger captures everything: read it from the connected
tool where one is connected (state the record count), or take it by paste-back where nothing is
connected, and label the provenance either way. Never invent a baseline to make the ledger look
complete.

## 5. What stays roadmap

The counsel-gated outcome beacon (cross-buyer, k-anonymous aggregates) stays on the roadmap
until legal clears it. Until then, benchmarks remain the shipped tables in
`diagnose/benchmarks.md` with hedged provenance, and this file never sources a benchmark
number or a sample size. Instrument the buyer's own system; do not pool their outcomes into a
network until the consent design ships.

## 6. Surface honesty

- **Claude Code and Codex:** the field writes, UTM enforcement, and baseline capture persist,
 and the results routines diff against the baseline on a schedule.
- **claude.ai:** the fields and UTM taxonomy still get built in the buyer's CRM, but there is
 no routines layer to capture outcomes automatically; baseline and subsequent readings come by
 paste-back in-session. Say so plainly rather than implying automatic capture.

The rule is one line: a system is not shipped until it is instrumented, because a system you
cannot measure is a system the Engineer cannot learn from.
