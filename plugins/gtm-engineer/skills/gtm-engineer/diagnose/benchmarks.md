# Benchmark Reference (DIAGNOSE mode, single source of truth)

This is the **one** benchmark file for the whole diagnostic. Every analyst sub-agent and the DIAGNOSE playbook read their numbers from here instead of carrying their own copies, so the quarterly re-tune is a one-file edit and the tables can never drift out of lockstep. If any other file appears to state a benchmark number, this file wins.

Artemis re-tunes these each quarter, so a freshly downloaded bundle always beats memory. The values below are the canonical shipped copy: every benchmark here is directional, drawn from patterns across a large body of B2B SaaS GTM audits and public industry sources, not a controlled study.

Provenance note: every number here is a **benchmark** (a labeled directional benchmark), never a buyer figure and never a leak by itself. A field the buyer fills from this table is "at benchmark" by definition and is never graded as a leak (that would be circular). See the DIAGNOSE playbook's provenance rule.

---

## 1. The grade thresholds (how a metric becomes a leak)

For each metric the buyer actually provided (not benchmark-filled), compute a ratio against the industry x motion benchmark, oriented so higher is always better:

- **Higher-is-better** (win rate, lead-to-opp, trial-to-paid, activation, expansion, quota attainment, meetings/SDR, demo-to-proposal, pipeline coverage, PQL-to-SQL): ratio = buyer / benchmark
- **Lower-is-better** (sales cycle): ratio = benchmark / buyer

Then grade:

| Ratio | Grade | What we do |
|-------|-------|-----------|
| **> 1.3** | Above average | HEALTHY, name it as a strength, do NOT leak it |
| **0.7 to 1.3** | At benchmark | FINE, note it, move on |
| **< 0.7** | Below average | LEAK CANDIDATE, this is leaking money, quantify it |

A metric below 0.7x its benchmark is the threshold for a confirmed leak. Above 1.3x is a strength worth naming. Inside 0.7-1.3x is just fine; do not pathologize average.

---

## 2. Core benchmarks (industry, applies across motions)

Deal size $ / cycle days / win % / lead-to-opp % / pipeline coverage x / monthly leads. Match the buyer's industry row; "Other" or unrecognized uses `default`.

| Industry | Deal | Cycle | Win | Lead->Opp | Pipe cov | Leads/mo |
|----------|------|-------|-----|-----------|----------|----------|
| SaaS | $15K | 45 | 25% | 15% | 3.5x | 300 |
| FinTech | $35K | 75 | 20% | 12% | 4.0x | 200 |
| HR Tech | $12K | 35 | 28% | 18% | 3.0x | 350 |
| MarTech | $20K | 50 | 22% | 14% | 3.5x | 280 |
| Healthcare | $50K | 100 | 18% | 10% | 4.5x | 150 |
| EdTech | $10K | 40 | 25% | 16% | 3.0x | 400 |
| E-commerce | $12K | 25 | 30% | 20% | 2.5x | 500 |
| DevTools | $25K | 50 | 24% | 15% | 3.5x | 250 |
| Cybersecurity | $45K | 80 | 20% | 12% | 4.0x | 180 |
| AI/ML | $30K | 60 | 22% | 13% | 3.5x | 220 |
| default | $20K | 50 | 23% | 15% | 3.5x | 300 |

---

## 3. Motion-specific benchmarks

Match the motion table to the buyer's motion. The motion guardrail applies: only benchmark the metrics that motion actually runs (see the DIAGNOSE playbook).

**PLG** (trial-to-paid % / activation % / expansion %):
SaaS 7/40/30 · FinTech 5/35/25 · HR Tech 8/45/35 · MarTech 6/38/28 · Healthcare 4/32/20 · EdTech 6/42/25 · E-commerce 10/50/40 · DevTools 8/45/35 · Cybersecurity 5/35/25 · AI/ML 6/38/30 · default 6/40/28

**SLG** (meetings/SDR-mo / demo-to-proposal %):
SaaS 12/50 · FinTech 10/45 · HR Tech 14/55 · MarTech 12/48 · Healthcare 8/40 · EdTech 14/52 · E-commerce 15/55 · DevTools 12/50 · Cybersecurity 10/45 · AI/ML 11/48 · default 12/50

**Hybrid** (PQL-to-SQL % / sales-assisted %):
SaaS 25/40 · FinTech 20/55 · HR Tech 28/35 · MarTech 22/45 · Healthcare 18/60 · EdTech 25/35 · E-commerce 30/30 · DevTools 28/35 · Cybersecurity 20/55 · AI/ML 24/45 · default 24/42

SLG and Hybrid also carry their own deal-size / cycle / team rows; use the SLG/Hybrid values when the motion is SLG/Hybrid, and the Core row only as a fallback. Where this copy does not carry a per-motion deal/cycle/team number, use the Core row and say so.

---

## 4. Signal-tier model (grade the buying-signal stack)

The tier is the highest any single tool reaches. One Gold tool makes the stack Gold even if the rest is Bronze.

| Tier | Grade | Tools | What it means |
|------|-------|-------|---------------|
| Tier 1 | **Gold** | Koala, Clay, Warmly, Amplemarket | Real-time fit + intent, activatable signals |
| Tier 2 | **Silver** | Apollo, Demandbase, ZoomInfo | Good data, some intent, weaker real-time activation |
| Tier 3 | **Bronze** | HubSpot Breeze Intelligence (formerly Clearbit), 6sense, Bombora | Intent feeds, thinner activation layer |
| Tier 0 | **No-Tools** | none of the above | Flying blind on buying signals |

A legacy Clearbit install still grades Bronze; note that Clearbit is no longer sold standalone (absorbed into HubSpot as Breeze Intelligence), so that stack cannot be expanded on that contract. A No-Tools company has a structural ceiling on how fast it can ever respond, because you cannot speed-to-lead a signal you never captured.

---

## 5. GTM Health Score bands (0-100)

This is the ONE canonical band scheme for the whole engagement (same 80/60/40 boundaries as the flash-audit gauge on the site). The leak-report template uses these exact four bands; never use any other banding.

| Score | Band | Color | Read |
|-------|------|-------|------|
| **80-100** | Healthy | green | Strong motion, minor tuning |
| **60-79** | Solid, leaking | lime | Working motion with 1-2 real leaks |
| **40-59** | At risk | amber | Multiple leaks, material revenue loss |
| **0-39** | Critical | red | Structural gaps; the motion is the problem |
