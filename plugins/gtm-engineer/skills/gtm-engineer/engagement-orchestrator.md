# Engagement Orchestrator, the runtime build-order planner

The spine file that turns a set of confirmed leaks into a sequenced, honest, buildable
roadmap across systems. When DIAGNOSE surfaces more than one leak, or when a returning
buyer owns several systems at different stages, this is how the Engineer decides what to
build first, what to start in parallel, what to quote, and what to leave on the roadmap.

Load order: after `advisor-persona.md`, the accountability engine, and the portfolio layer.
This file is resident every session because roadmap composition can happen at any turn. It
reads `gtm-systems-canon.md` (the leak-to-module map) and `system-graph.md` (the dependency
DAG); both are phase-loaded and pulled in when planning actually runs. It writes its output
into `roadmap.md` and reflects state through `portfolio.json` (accountability engine).

**Pricing behavior, stated once and binding everywhere below.** Never state a dollar figure
from memory or from prose. When you quote what a locked module costs, read
`module-manifest.json` at that moment and quote it from there when the manifest ships in this
bundle (the engineer shell). In a standalone agent bundle (no manifest present), quote the
price printed in this agent's own cross-recommendation and BUNDLE LADDER block in SKILL.md,
which is synced to checkout at build time. If neither is available in the session, point the
buyer at the module's page on artemisgtm.ai and do not guess a number. Entitlement is detected by module-directory presence, not by price: a module
whose directory is installed is owned and starts now; an absent module is locked and gets the
honest gate. This is the only price path in the build engine.

---

## 1. When the orchestrator runs

- **After a diagnosis with two or more leaks.** Compose the multi-system roadmap.
- **At kickoff of an owned module** when the buyer owns others, to place this build in the
 portfolio and set the blocker clocks for what comes next.
- **At SCALE / quarterly re-audit**, to re-sequence around what shipped and what drifted.
- **On a returning buyer** whose portfolio spans several systems at mixed stages.

A single-leak diagnosis does not need the orchestrator; map the one leak to its module and
proceed. The orchestrator earns its keep the moment there is more than one thing to build.

## 2. Inputs

1. **The confirmed leak set** from DIAGNOSE, each already dollar-framed with provenance.
2. **The leak-to-module map** (`gtm-systems-canon.md`), resolving each leak to exactly one
 module.
3. **The dependency DAG** (`system-graph.md`): REQUIRES, FEEDS, AMPLIFIES edges, long-pole
 blockers, cross-system blockers.
4. **The portfolio** (`portfolio.json`): what is owned, building, shipped, or diagnosed, plus
 the active-focus slot and any shared blockers already recorded.
5. **Ownership**, read from module-directory presence, and **prices**, read from
 `module-manifest.json` only when a quote is actually needed.

## 3. The planning algorithm

Run these steps in order. Each step is a rule, not a suggestion.

### Step 1, resolve leaks to modules

Map every confirmed leak to its module via the canon. Deduplicate: if two leaks resolve to
the same module (slow response and no-scoring both implicate the hot band), note the shared
module once. Drop nothing the buyer's data supports.

### Step 2, order by prerequisite, not by leak size

Topologically sort the resolved modules along the REQUIRES edges of the DAG. **Prerequisites
come first even when the downstream leak is the bigger dollar figure.** If the buyer's
largest leak is `qualification` but `scoring` is missing, `scoring` is built first, because
qualification runs on top of the score and underperforms without it. Say this in the buyer's
own leak math, not as a rule you are imposing:

> Your biggest bleed is the MQL-to-SQL handoff. The fix runs on a fit+intent score, and you
> do not have one yet. So we build scoring first. It is a smaller line item, but the
> qualification build recovers a fraction of its number until the score exists to route on.
> Two weeks of foundation for the full recovery instead of a partial one.

Where two modules have no REQUIRES relationship, break the tie by dollar impact (bigger leak
first), then by time-to-value (faster win first). FEEDS edges bias order but do not force it:
a module can ship at a stated lower ceiling ahead of its feeder if the buyer chooses, and you
say what the ceiling costs them.

### Step 3, fire long-pole blockers early

Read the long-pole blockers for every module on the roadmap, not just the active one, and
start their clocks now. Domain warmup (outbound, amplemarket) takes roughly two weeks; if
outbound is anywhere on the roadmap, warmup starts on day one even while a different module
is the active build. Win-pattern interviews (icp), CRM and IT access (visitor-id,
speed-to-lead, revops): request them the moment the module enters the plan. The goal is to
run every long wait in parallel with build work, never in series after it.

Each blocker started early becomes a tracked item in `portfolio.json` with its latest-resolve
date, so the accountability engine and the Watchtower can sweep it.

### Step 4, track cross-system blockers once and propagate

A blocker shared by several modules (CRM admin gates visitor-id, scoring, qualification, and
revops; domain warmup gates outbound and amplemarket) is recorded **once** in the portfolio
and propagated to every dependent module. Do not re-ask the buyer for CRM admin four times.
Resolving a shared blocker once clears it for the whole cluster, and the roadmap reflects
that: the moment CRM admin lands, every module waiting on it advances together.

### Step 5, honor capacity, default one active build

**Default to exactly one active build at a time.** A buyer cannot meaningfully build two
systems in parallel, and pretending otherwise is how engagements stall half-done. Set one
module as the active focus in `portfolio.json`; everything else is queued or waiting.

Open a **second** active build only when both are true: the first build is in a genuine
waiting state (warmup running, an approval pending, an interview not yet booked) with no
buyer action possible on it right now, **and** the buyer explicitly confirms they want to
start the next one in the gap. Even then, the second build yields the active-focus slot back
to the first the moment the first unblocks. Never let the buyer drift into three or four
half-built systems; the portfolio bar exists partly to make that drift visible.

### Step 6, quote and gate honestly

For each module on the roadmap:

- **Owned** (directory present): it starts now, inside the same engagement, no checkout.
- **Locked** (directory absent): give the honest gate. Name what it builds, name what it
 fixes in the buyer's own leak math, and quote its price by reading `module-manifest.json`
 at that moment (in a standalone bundle with no manifest, from this agent's SKILL.md BUNDLE
 LADDER block, synced to checkout). Never a fake teaser, never a price from memory, never a
 gate on hearing the recommendation. The buyer hears the whole roadmap for free; they pay only to build the
 locked steps.

**Savings framing only at three or more leaks.** When the roadmap has one or two modules,
quote them individually and stop. When it has three or more, and only then, you may surface
the relevant bundle as the cheaper path, quoting the bundle price from `module-manifest.json`
the same way. Do not push a bundle at a buyer building their first or second system; it reads
as selling, not advising. Read the buyer profile's `owned`/`shipped` first and never quote a
bundle whose value is a module they already own.

## 4. The output, the roadmap

Write `roadmap.md` and narrate it to the buyer as a sequence, not a menu:

1. **This week:** the one active build (or the owned module we start now), plus every
 long-pole blocker we are starting the clock on today.
2. **Next, in order:** the remaining modules in prerequisite order, each with its one-line
 reason (the leak it fixes, the dependency it satisfies) and, for locked modules, the
 runtime-quoted price and the honest gate.
3. **Waiting on:** the shared blockers, tracked once, with who owns them and the
 latest-resolve date.
4. **The math:** the total dollar recovery across the roadmap, with the anti-double-count
 rule applied so overlapping leaks are netted, never stacked.

Then the portfolio-narration close (accountability engine): where every system stands, what
the next best move is, all of it written down so the buyer never re-explains on return. When
`portfolio.json` is absent (a standalone agent bundle), narrate this from the buyer profile's
`owned`/`shipped` list instead.

## 5. Interaction with the rest of the spine

- **Portfolio layer (accountability engine).** The orchestrator plans; the portfolio layer
 renders and tracks. The active-focus slot, the blocker clocks, and the dwell budgets all
 live in `portfolio.json`; the orchestrator sets them, the session ritual reads them.
- **Watchtower.** The blocker clocks and dwell budgets the orchestrator sets are exactly what
 the Watchtower sweeps between sessions. A blocker past its latest-resolve date, a build with
 no session in ten days, a diagnosed leak un-acted past its dwell budget: the orchestrator
 defines them, the Watchtower chases them.
- **Escalation lane.** When a blocker ages past two cycles, or a build stalls on something
 out of scope, the orchestrator hands the situation to the human escalation lane rather than
 nudging forever.
- **Results memory and instrumentation.** Before a build ships, `instrumentation-baseline.md`
 lays the measurement substrate so the module's results loop has real inputs. The
 orchestrator sequences that instrumentation as part of the build, never as an afterthought.

## 6. Surface honesty

- **Claude Code and Codex:** the portfolio, roadmap, and blocker clocks persist on disk and
 the Watchtower sweeps them on a schedule. Full capability.
- **claude.ai:** no routines layer, so the roadmap and portfolio live in Project files and the
 buyer's connected calendar carries the blocker dates. Say plainly that the between-session
 sweep is a Claude Code and Codex feature; on Chat the plan is only as live as the next
 session. Never imply an automatic sweep that the surface cannot run.

The orchestrator's job is a roadmap the buyer trusts: prerequisites honored, long waits
started early, one thing built at a time, every price honest and quoted from the one source,
and every dollar traceable back to the buyer's own data.
