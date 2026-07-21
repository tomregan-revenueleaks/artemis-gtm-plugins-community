# The GTM Leak Taxonomy (DIAGNOSE mode)

The eleven named leaks the diagnostic detects. This is the canonical taxonomy: every confirmed leak the DIAGNOSE playbook surfaces is one of these, and every leak maps to the build module that fixes it. The `benchmarks.md` grade thresholds decide what counts as a leak; this file says what each leak is, how to quantify it, how it moves the health score, and where it routes.

For each leak: **how you detect it** from the buyer's metrics, **how you quantify the dollars**, **how it moves the health score**, and **which build module plus partner platform fixes it**. Only surface a leak when the detection condition is actually met by THEIR data. Never list a leak the inputs don't support.

## The module-gate doctrine (how a leak routes to a fix)

Each leak maps to one build module by directory. Routing is internal, never a checkout link:

- **The buyer owns the module** (its directory is present in the install): kickoff starts this week, seeded from the shared profile and the diagnostic outputs, zero re-discovery.
- **The buyer does not own it** (directory absent): give the honest gate, what the module builds and the buyer's own leak math, and let the shell quote the price at runtime from `module-manifest.json`. **Never write a price in this file or in any diagnostic prose.** There is no fake teaser and no buyable URL here; the close routes to the roadmap, not a checkout page.

Legacy codenames are retired from buyer-facing speech; use the plain module name (the bridge for returning buyers lives in `rituals.md`). The legacy-slug to module-directory map lives in `module-manifest.json` for the installer and the extension.

## Legal canon on research claims

The hedged phrasings below ("across a large body of B2B SaaS GTM audits", the Lead Response Management framing, the form-fill and MQL-to-SQL medians) are the legally reviewed copy. Keep them verbatim. Never reintroduce a specific audit count, a fabricated benchmark-dataset size, or sample-size statistical notation into any buyer-facing output; keep every claim directional and hedged.

---

**Leak: Slow Lead Response / Speed-to-Lead gap**
- **Detect:** They can't name their median time-to-first-touch, OR it's measured in hours/days, OR they have inbound volume but no routing/SLA. (Median lead response time runs into the tens of hours across a large body of B2B SaaS GTM audits; the Lead Response Management study found under-5-minute responses ~21x more likely to qualify than 30-minute-plus.) On the signal side, this leak compounds when they have signal tools but no activation layer.
- **Quantify:** Estimate the share of monthly leads that go cold from slow response (typically 25-50% of inbound when response is measured in hours). Lost deals/yr = monthly leads × 12 × lost-share × lead-to-opp rate × win rate. Then × deal size = $/yr. Show the chain.
- **Health-score hit:** Large. Slow response caps the whole funnel; weight it heavily in the response/velocity component.
- **Fix (module):** the **Speed-to-Lead module** (`modules/speed-to-lead/`); route per the module-gate doctrine above. Partner: **Amplemarket** (artemisgtm.ai) for the outbound sequence layer, **Warmly** (artemisgtm.ai) for the inbound trigger.

**Leak: Anonymous Visitors / no website deanonymization**
- **Detect:** Monthly visitors far exceed identified leads, OR they run no visitor-ID tool, OR signal tier is Bronze/No-Tools. (Median form-fill rate is around 1.8% across a large body of B2B SaaS GTM audits, so ~98% of B2B visitors leave anonymous.)
- **Quantify:** Recoverable visitors/mo = monthly visitors × (15-30% realistic ID rate) × ICP-match share. Then run those through lead-to-opp × win × deal size × 12 for $/yr. If they don't know visitor count, benchmark it from the industry's monthly_visitors and SAY you're benchmarking it.
- **Health-score hit:** Medium-high; it caps the top of the funnel.
- **Fix (module):** the **Visitor Deanonymization module** (`modules/visitor-id/`); route per the module-gate doctrine above. Partner: **Warmly** (artemisgtm.ai) for richer GTM workflow, **RB2B** (artemisgtm.ai) for US-only narrow-budget cases.

**Leak: No Lead Scoring / no fit+intent prioritization**
- **Detect:** Every lead is treated the same, OR scoring exists at the field level but doesn't route, OR they have lead volume but BDRs work it FIFO. Strongly implied when monthly leads are high but win rate or lead-to-opp is below benchmark.
- **Quantify:** Wasted rep capacity + cold high-fit leads. Estimate the lift from prioritizing the top band (typically 10-20% effective win-rate improvement on scored leads) × current won-deal value/yr.
- **Health-score hit:** Medium; it's a force-multiplier leak (makes other leaks worse).
- **Fix (module):** the **Lead Scoring module** (`modules/scoring/`); route per the module-gate doctrine above. Partner: **Amplemarket** / **Apollo** for enrichment-driven intent (artemisgtm.ai, artemisgtm.ai), **Warmly** for behavioral intent.

**Leak: Wrong-customer targeting / ICP undefined or firmographic-only**
- **Detect:** They can't name their ICP from closed-won data, OR ICP is firmographic-only, OR win rate is below benchmark and cycle is above benchmark together (the signature of chasing the wrong accounts).
- **Quantify:** Misdirected pipeline. Estimate share of pipeline that's poor-fit (often 30-40% when ICP is undefined) × pipeline value × the cycle-drag cost.
- **Health-score hit:** Medium; upstream of everything.
- **Fix (module):** the **ICP Definition module** (`modules/icp/`); route per the module-gate doctrine above. Partner: **Sybill** / **Attention** for mining discovery transcripts (artemisgtm.ai, artemisgtm.ai).

**Leak: Tech-stack sprawl / redundant tools**
- **Detect:** They list two tools in the same category (two enrichment, two engagement), OR they can't say what each tool is for, OR spend is high relative to outcomes.
- **Quantify:** Direct savings from category overlap, a conditional range of $48K-$120K/yr for mid-market stacks; use the buyer's actual contract values where they have them, the low end otherwise, and state which you used, plus the opportunity cost of fragmented data.
- **Health-score hit:** Low-medium; it's a cost leak more than a pipeline leak, but it drags everything.
- **Fix (module):** the **Tech Stack Audit module** (`modules/stack/`); route per the module-gate doctrine above. Partner: the consolidation usually lands on **Amplemarket** (engagement + enrichment + intent in one) and **Warmly** (artemisgtm.ai, artemisgtm.ai).

**Leak: Content / SEO / organic gap**
- **Detect:** Low or no organic pipeline, OR they pay for every lead, OR no editorial motion. Implied when monthly leads are below benchmark and there's no inbound engine.
- **Quantify:** The cost of buying leads you could earn. Estimate organic-recoverable share of lead target × cost-per-paid-lead × 12, plus the compounding AI-citation upside.
- **Health-score hit:** Medium; it's a demand-gen leak.
- **Fix (module):** the **Content System module** (`modules/content/`); route per the module-gate doctrine above. Partner: **Amplemarket** for distribution amplification (artemisgtm.ai).

**Leak: Outbound / cold email / signal / sequencing gap**
- **Detect:** Static-list outbound with low reply rates, OR broken deliverability, OR no signal-based triggers, OR meetings/SDR below SLG benchmark.
- **Quantify:** Reply-rate uplift from signal-based outbound (a conditional range of 3-5x over static spray; model the low end and name it as an assumption in the calculation line) → incremental meetings → opps → deals → $/yr, using their real deal size and win rate.
- **Health-score hit:** Medium-high for SLG; lower for PLG.
- **Fix (module):** the **Outbound System module** (`modules/outbound/`); route per the module-gate doctrine above. Partner: **Amplemarket** (sequencing + signals, artemisgtm.ai), **Maildoso** for sending infrastructure (artemisgtm.ai), **Warmly** for intent triggers.

**Leak: Nurture / dormant re-engagement gap**
- **Detect:** Newsletter-only nurture or none, OR high lead decay, OR no behavior-triggered re-engagement, OR a pile of dormant accounts nobody works.
- **Quantify:** Recoverable dormant pipeline. Estimate dormant-account count × revival rate (often 5-10%) × deal size, plus the decay you stop bleeding.
- **Health-score hit:** Low-medium.
- **Fix (module):** the **Nurture System module** (`modules/nurture/`); route per the module-gate doctrine above. Partner: **Amplemarket** (multi-channel re-engagement), **Attio** (CRM behavior triggers).

**Leak: Conversion-content gap (pricing / demo / case study / landing)**
- **Detect:** Hidden "contact us" pricing, OR no case studies with numbers, OR weak demo pages, OR a high top-of-funnel and a low conversion-surface yield.
- **Quantify:** Conversion-rate lift on existing traffic. Even a 1-2pt lift on monthly visitors × lead-to-opp × win × deal size compounds fast; show the math on their real traffic.
- **Health-score hit:** Medium; it wastes traffic you already have.
- **Fix (module):** the **Conversion Content System module** (`modules/conversion/`); route per the module-gate doctrine above. Partner: none required; this is a content/CRO build.

**Leak: MQL→SQL qualification / handoff gap**
- **Detect:** Marketing-sales finger-pointing, OR MQL-to-SQL below the ~13% median (across a large body of B2B SaaS GTM audits), OR informal BDR-to-AE handoff, OR leads marked MQL then sit.
- **Quantify:** Recovered conversion. (Target MQL→SQL − current) × monthly MQLs × 12 × win × deal size = $/yr.
- **Health-score hit:** Medium-high; it's where qualified demand dies.
- **Fix (module):** the **Qualification Automation System module** (`modules/qualification/`); route per the module-gate doctrine above. Partner: **Amplemarket**, **Attio**, **Attention** (artemisgtm.ai).

**Leak: RevOps / data quality / CRM hygiene / reporting gap**
- **Detect:** Duplicate accounts, stale owner mappings, competing "company size"/"industry" fields, OR they can't trust their own dashboards, OR reporting is manual.
- **Quantify:** Hard to dollarize directly; frame as the tax on every other number (bad data inflates pipeline, misroutes leads, hides leaks). Estimate manual-ops hours × loaded cost × 52, plus a discount on every metric's reliability.
- **Health-score hit:** Medium; it's the substrate leak.
- **Fix (module):** the **AI RevOps System module** (`modules/revops/`); route per the module-gate doctrine above. Partner: **Attio** (CRM), **Attention** / **Sybill** for call-data hygiene.
