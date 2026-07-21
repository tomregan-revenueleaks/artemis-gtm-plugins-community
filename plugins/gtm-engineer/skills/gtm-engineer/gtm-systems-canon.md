# GTM Systems Canon, the six-systems model and the leak framework

Shared canon for The AI GTM Engineer. This is the one authoritative definition of
the revenue engine the Engineer teaches, diagnoses, and builds: what the systems are,
how they relate, and the named leaks that map every diagnostic finding to the module
that fixes it. The Academy (`academy/engine-map.md`) teaches this model in buyer
language; this file is the reference every mode reads from so the map never forks.

Phase-loaded: pulled in at LEARN moments and whenever the Engineer composes a
multi-system roadmap. It costs nothing resident. It pairs with `system-graph.md`
(the dependency DAG between systems) and `engagement-orchestrator.md` (the runtime
build-order planner). Benchmarks live once in `diagnose/benchmarks.md` in the engineer
shell; a standalone agent bundle carries the same benchmark tables inside its own
`playbook.md`. Read whichever ships beside this file; they are the same canon. This file
never restates a benchmark value.

**Never state a dollar price in this file or from memory.** Module prices are quoted
at runtime by reading `module-manifest.json`. When you name what a module costs, read
it there; if you cannot, point the buyer at the module's page on artemisgtm.ai and do
not guess.

---

## 1. The revenue engine in four layers

A B2B revenue engine is not a list of tools. It is a layered system where each layer
depends on the one beneath it. Diagnose and build in layer order, because a leak in a
lower layer caps every layer above it.

1. **Foundation, who you sell to.** The Pain-First ICP. Everything downstream fires on
 the wrong accounts if this is undefined or firmographic-only. Module: `icp`.
2. **Capture and create demand.** How prospects arrive and get engaged: inbound content,
 outbound prospecting, website visitor identification, database nurture. Modules:
 `content`, `outbound`, `visitor-id`, `nurture`.
3. **Convert demand to pipeline.** How captured interest becomes qualified pipeline:
 lead scoring and prioritization, decision-stage content, MQL-to-SQL qualification,
 speed of first response. Modules: `scoring`, `conversion`, `qualification`,
 `speed-to-lead`.
4. **Operate the engine.** The substrate that keeps the other three honest: tech-stack
 rationalization, CRM hygiene, agentic RevOps automation, and the tool-specific
 implementation craft. Modules: `stack`, `revops`, `amplemarket`.

## 2. The six core systems

The center of the model is six systems. These are the "system" tier: full engine builds,
not point fixes.

- **Content.** The organic and AI-citation engine: pillars mapped to ICP problems,
 SEO/AEO/GEO structure, attribution capture. Feeds nurture and conversion. Module: `content`.
- **Outbound.** Signal-based prospecting: deliverability infrastructure, signal-to-sequence
 mapping, copy that earns replies. Fires on ICP-matched accounts. Module: `outbound`.
- **Nurture.** Behavior-triggered re-engagement of the database: stage-transition logic,
 dormant-account revival, job-change re-engagement. Module: `nurture`.
- **Conversion.** Decision-stage content: pricing transparency, demo-gate calibration,
 case studies with numbers, the ROI-calculator surface. Module: `conversion`.
- **Qualification.** The MQL-to-SQL machine: framework selection, BDR queue, handoff
 auditing, the disqualification policy. Runs on top of scoring. Module: `qualification`.
- **AI RevOps.** The automation layer: CRM hygiene, agentic workflows, reporting, the
 human-in-the-loop escalation canon. Amplifies whatever is already built. Module: `revops`.

## 3. The foundational and tactical layers

Around the six systems sit the modules that feed or accelerate them.

- **ICP Definition** (`icp`). The foundation. The 7-dimension Pain-First scorecard is the
 artifact every downstream system reads: scoring anchors its fit model to it, outbound
 filters signals through it, content maps pillars to its pains, qualification inherits its
 disqualification logic. Build this first when it is missing or firmographic-only.
- **Lead Scoring** (`scoring`). Fit plus intent prioritization. The hinge between capture
 and conversion: qualification, speed-to-lead's hot band, and nurture re-engagement all
 read the score.
- **Visitor Deanonymization** (`visitor-id`). Turns anonymous website traffic into named,
 ICP-matched accounts. The intent input for speed-to-lead, outbound, and scoring.
- **Speed-to-Lead** (`speed-to-lead`). The action layer: routing plus a sub-five-minute SLA
 on the hot band. Caps out at form-fill speed without visitor-id feeding it identified
 accounts, and treats every lead the same without scoring naming the hot band.
- **Tech Stack Audit** (`stack`). Rationalizes the tooling before RevOps automates it.
 A bloated stack becomes bloated automation.
- **Amplemarket Implementation** (`amplemarket`). The tool-specific onboarding craft under
 outbound's ownership, and the one production-proven EXECUTE lane at launch.

## 4. The leak framework

Every diagnostic finding resolves to one of eleven named leaks. Each leak has a detection
signature (a below-benchmark metric or a structural gap), a dollar-quantification method,
and exactly one module that fixes it. A leak is only surfaced when the buyer's own data
meets the detection condition; never list a leak the inputs do not support.

| Leak | Detection signature | Fixed by |
|---|---|---|
| Slow lead response | No SLA, response in hours/days, volume without routing | `speed-to-lead` |
| Anonymous visitors | Visitors far exceed identified leads, no visitor-ID tool | `visitor-id` |
| No lead scoring | Every lead worked the same, BDRs FIFO, no fit+intent routing | `scoring` |
| Wrong-customer targeting | ICP undefined or firmographic-only, low win rate with long cycle | `icp` |
| Tech-stack sprawl | Two tools per category, unclear ownership, spend high vs outcomes | `stack` |
| Content / SEO gap | Low organic pipeline, pays for every lead, no editorial motion | `content` |
| Outbound gap | Static-list spray, broken deliverability, no signal triggers | `outbound` |
| Nurture gap | Newsletter-only or no nurture, high decay, dormant pile | `nurture` |
| Conversion-content gap | Hidden pricing, no case studies with numbers, weak demo pages | `conversion` |
| MQL-to-SQL qualification | Marketing-sales finger-pointing, below-median MQL-to-SQL, informal handoff | `qualification` |
| RevOps / data quality | Duplicate records, competing fields, untrusted dashboards, manual reporting | `revops` |

The Amplemarket module is not a distinct leak; it is the implementation layer for the
outbound leak, offered when the outbound build lands on Amplemarket.

## 5. Leak-quantification and honesty rules (canon, non-negotiable)

These move verbatim from the diagnostic engine and bind every mode that touches a leak.

- **No leak, no recommendation.** Surface a leak only when the buyer's real data meets the
 detection condition. Never manufacture a leak to justify a module.
- **Use their real numbers.** Every dollar figure chains off metrics the buyer gave you.
 When a figure needs a number they did not provide, benchmark it from
 `diagnose/benchmarks.md` in the engineer shell (or the same benchmark tables in this agent's
 own `playbook.md` in a standalone bundle) and say so in the same breath (the benchmark-fill
 circularity guard). A benchmark substitution is labeled, never itself counted as a leak.
- **Show the calculation.** Every leak carries a one-line formula. A dollar figure with no
 chain is a number the buyer cannot defend to their CFO.
- **Conservative beats impressive.** Use the low end of any range. One inflated number loses
 the buyer.
- **Never double-count.** When two leaks attack the same lost-deal pool, note the overlap and
 net it out of the total. The anti-double-count rule is a hard check in the total.
- **Motion guardrail.** Weight leaks by the buyer's motion (PLG, SLG, hybrid). An outbound
 leak weighs heavily for SLG and lightly for PLG; do not apply one motion's weights to
 another.

## 6. How the modes use this canon

- **LEARN** teaches the four layers and the six systems, sequenced by the buyer's stage,
 via the Academy. This file is the source the Academy is written from.
- **DIAGNOSE** runs the leak framework against the buyer's data and produces the dollar-framed
 readout with provenance on every figure.
- **BUILD** consumes the layer order and the per-module ownership when the orchestrator
 composes the roadmap (`engagement-orchestrator.md` reads this plus `system-graph.md`).
- **OPERATE** and **SCALE** re-read the leak framework at re-audit time to detect drift and
 rank the next un-built system.

The map is one map. When a benchmark, a leak definition, or a module ownership changes, it
changes here and in the engineer's `diagnose/benchmarks.md` once (standalone playbooks inherit
at the next bundle rebuild), and every mode inherits it.
