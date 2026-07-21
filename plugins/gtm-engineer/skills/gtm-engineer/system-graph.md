# System Graph, the dependency DAG between the twelve modules

The build-order truth table for The AI GTM Engineer. `gtm-systems-canon.md` says what
each system is; this file says what each system needs before it can be built well, and
which downstream systems it sharpens. `engagement-orchestrator.md` reads this graph at
runtime to sequence a multi-system roadmap. Derived from the cross-sell pairings and each
module's stated prerequisites; when a prerequisite changes in a module playbook, update the
edge here so the planner never sequences on stale dependencies.

Phase-loaded at orchestrator planning moments only. No resident cost.

---

## Edge legend

Three edge types, strongest first. The planner treats them differently (see the
orchestrator's prerequisite rule).

- **REQUIRES (hard prerequisite).** The downstream system cannot be built credibly without
 the upstream one. Build the upstream first even when the downstream leak is bigger.
- **FEEDS (soft input).** The upstream produces an artifact or signal that materially
 sharpens the downstream. Build order prefers upstream-first, but the downstream can ship
 standalone at reduced ceiling; say so honestly.
- **AMPLIFIES (automation overlay).** The system automates or optimizes an already-built
 system. Sequence it after the system it operates on.

## The nodes and their edges

```
FOUNDATION
 icp REQUIRES: (none, foundational)
 FEEDS: scoring, content, outbound, qualification (via scoring),
 nurture (via content), conversion (via content), visitor-id filters

CAPTURE / CREATE
 visitor-id REQUIRES: (none; stands alone)
 FEEDS: speed-to-lead, outbound, scoring (intent layer)
 content REQUIRES: icp
 FEEDS: nurture, conversion
 outbound REQUIRES: icp
 FEEDS: scoring (reply routing)
 FED BY: visitor-id (intent)
 amplemarket REQUIRES: outbound (the motion it implements; or a standalone standup)
 the EXECUTE lane for the outbound leak

CONVERT
 scoring REQUIRES: icp
 FED BY: visitor-id (intent signals)
 FEEDS: qualification, speed-to-lead (hot band), nurture, conversion
 speed-to-lead FED BY: visitor-id (identified accounts), scoring (hot band)
 ships standalone on form-fills, at a lower ceiling
 qualification REQUIRES: scoring
 conversion REQUIRES: content
 FEEDS: qualification (demo handoff), scoring (calculator intent)
 nurture REQUIRES: content
 FED BY: scoring (re-engagement intent)

OPERATE
 stack REQUIRES: (none, foundational)
 FEEDS: revops; recommends the system module per surfaced gap
 revops REQUIRES: stack (rationalize before automating)
 AMPLIFIES: any built system (CRM hygiene, reporting, handoff automation)
```

## Reading the graph as layers

A valid build order is any topological sort of the REQUIRES edges. The natural layering:

1. **Layer 0, foundation:** `icp`, `stack`. No prerequisites. When either leaks, it is
 almost always the first build, because it caps everything above it.
2. **Layer 1, inputs:** `visitor-id`, `content`, `scoring`. Each needs at most `icp`.
 `scoring` and `content` hard-require `icp`; `visitor-id` is independent.
3. **Layer 2, motions and conversion:** `outbound`, `nurture`, `conversion`,
 `qualification`, `speed-to-lead`. Each needs a Layer 1 system feeding it.
4. **Layer 3, overlays:** `amplemarket` (implements the outbound motion), `revops`
 (automates any built system after the stack is rationalized).

## The hard prerequisites (never skip)

These are the REQUIRES edges the planner refuses to invert without saying so out loud:

- `scoring` REQUIRES `icp`. A fit model with no Pain-First ICP scores on the wrong signal.
- `qualification` REQUIRES `scoring`. A BDR queue with no score is just date-ordered.
- `content` REQUIRES `icp`. Pillars without an ICP are topical, not compounding.
- `nurture` REQUIRES `content`. Stage-triggered nurture has nothing to send without content.
- `conversion` REQUIRES `content`. Decision-stage content sits at the bottom of the funnel
 content fills.
- `revops` REQUIRES `stack`. Automating a bloated stack automates the bloat.

When a buyer's biggest-dollar leak is a downstream node whose hard prerequisite is missing,
sequence the prerequisite first and explain the reason in the buyer's own leak math: the
downstream fix underperforms until the foundation exists.

## Long-pole blockers (start the clock early)

Some nodes carry a blocker that takes real calendar time regardless of build effort. The
orchestrator fires these early, even before the module they gate becomes the active build,
so the wait runs in parallel instead of serial:

- `outbound` and `amplemarket`: **domain and mailbox warmup**, roughly two weeks. Start
 warmup on day one of any engagement that will touch outbound.
- `icp`: **win-pattern interview scheduling**. Closed-won interviews take days to book;
 send the requests as soon as ICP is on the roadmap.
- `visitor-id`, `speed-to-lead`, `revops`: **admin and IT access** (CRM admin, script
 install, MCP tokens). Request access the moment these enter the plan.
- `content`: no install blocker, but organic and AI-citation results compound over months;
 set the expectation that this is a slow-yield build, not a slow-to-ship one.

## Cross-system blockers (track once, propagate)

Some blockers are shared across multiple nodes: CRM admin access gates `visitor-id`,
`scoring`, `qualification`, and `revops`; domain warmup gates `outbound` and `amplemarket`.
Record a shared blocker once in `portfolio.json` and propagate it to every dependent node,
rather than re-surfacing it per module. Resolving it once unblocks the whole cluster.

## Artifacts that flow along FEEDS edges

The FEEDS edges are not abstract. They carry named artifacts the buyer already paid to
build, read from `shipped[].artifacts` in the buyer profile:

- the ICP scorecard flows from `icp` into `scoring`, `outbound`, `content`, and
 `qualification`;
- the fit+intent score flows from `scoring` into `qualification`, `speed-to-lead`, and
 `nurture`;
- identified accounts flow from `visitor-id` into `speed-to-lead`, `outbound`, `scoring`;
- the content pillar map flows from `content` into `nurture` and `conversion`.

Reuse the real artifact, never rebuild a generic version of what a prerequisite already
produced (`buyer-profile.md` step 2a). The graph is why the second module a buyer runs
feels like it already knows them.
