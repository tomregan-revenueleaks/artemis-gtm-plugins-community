# Stack Report Template (Stack Grader)

This is the deliverable. The whole engagement converges here: a prioritized, dollar-framed stack report the buyer screenshots and forwards to their team. Fill it from the Grade + Price + Recommend steps (playbook Steps 4-6). Every gap and every redundancy is a running observation made formal.

Keep the buyer's stack and numbers throughout. Don't pad. A CFO should be able to read this in three minutes and know exactly what's missing, what's redundant, what each costs, and what to do first.

---

## Section 1: Executive Header

```
═══════════════════════════════════════════════════
 SALES TECH STACK GRADE
 [Company Name] · [Growth Motion] · [Stage]
 Graded [Date]
═══════════════════════════════════════════════════

COVERAGE SCORE: [XX]/100 [Grade A-F], [label]
MATURITY TIER: [Starter / Foundation / Growth / Enterprise / Over-Engineered]
CATEGORIES COVERED: [N]/8 REDUNDANCY DRAG: −[X] pts
TOTAL GAP STAKE: $[X.X]M/year forgone across [N] gaps
REDUNDANT SPEND: $[Y]K/year wasted across [M] redundancies
COST OF DELAY (gaps): $[X]/week every week these stay open
```

**Letter-grade bands** (set from the 0-100 score, the same canonical bands as the live tool; never use any other banding):
- 90-100. A. Best-in-class.
- 80-89. B. Strong.
- 65-79. C. Average.
- 50-64. D. Below average.
- < 50. F. Critical gaps.

**Maturity-tier line**, one sentence on what the tier means for them:
> "Foundation tier: core tools in place. Adding coverage in your two missing must-have categories will accelerate your pipeline."

---

## Section 2: The Diagnosis (one paragraph)

Plain-language summary of where the stack is under-covered and where it's over-bought, in the advisor's voice. No jargon. Name the single biggest gap by dollars and the through-line.

> "Your foundation is solid. CRM, sales engagement, and enrichment are all covered. The leak is on the intent side: you're a PLG company with no Visitor ID, which is your single heaviest-weighted category at 22 points, so 98% of your in-market traffic walks out anonymous. And you're carrying three enrichment tools doing the same job. Fill the one gap, cut to one enrichment tool, and you move from a D to a B."

---

## Section 3: Coverage Map (all eight categories)

A line per category, in motion-weight order, so the buyer sees where every point sits.

```
COVERAGE MAP ([Motion] weights)
───────────────────────────────────────────────────
✅ CRM [weight] pts · covered · [tools]
✅ Sales Engagement [weight] pts · covered · [tools]
❌ Visitor Identification [weight] pts · GAP (must-have)
⚠️ Conversation Intelligence [weight] pts · GAP (should-have)
✅ Data Enrichment [weight] pts · covered · [tools] ⚠️ 3 tools, redundancy
...
───────────────────────────────────────────────────
Raw coverage [X] − redundancy drag [Y] = Coverage Score [Z]/100
```

Status follows the live tool: ✅ green = covered; ❌ red = uncovered must-have; ⚠️ yellow = uncovered should-have or nice-to-have. Mark any 3+-tool category as a redundancy inline.

---

## Section 4: Ranked Gaps (the core)

Rank by annual $ stake, descending. One block per confirmed gap. Number them so the buyer can reference "Gap #2" in a team thread.

```
🔴 GAP #1 · [Category] PRIORITY: Critical (missing must-have)
───────────────────────────────────────────────────
Coverage cost: [weight]-point gap at your [motion] motion
Annual stake: $[X]/year forgone
Cost of delay: $[X]/mo · $[X]/wk · $[X]/day
What you're missing: [1-2 sentences, plain language, what this category does]
The calculation: $[stage ARR] × [leak rate]% = ~$[X]/year forgone

FILL IT WITH
Partner: [Tool], [why this tool for THIS category].
 (If you already run [alternative] and it's working, keep it; the
 audit adapts to your stack.)
```

Repeat for every confirmed gap. Priority follows the stage map: missing must-have = Critical (🔴), missing should-have = Important (🟠), missing nice-to-have = Nice (🟡).

### Cost-of-delay math (on the total gap stake)
From the total gap stake: monthly = annual ÷ 12 · weekly = annual ÷ 52 · daily = annual ÷ 365. This is the urgency lever, "every week you wait, that's $[X] of forgone pipeline."

### The canonical category → leak rate (as shipped)

| Category | Leak rate | Gap dollars |
|----------|-----------|-------------|
| Sales Engagement | 5% | stage ARR × 0.05 |
| CRM | 4% | stage ARR × 0.04 |
| Visitor Identification | 4% | stage ARR × 0.04 |
| Data Enrichment | 3% | stage ARR × 0.03 |
| Conversation Intelligence | 3% | stage ARR × 0.03 |
| Revenue Intelligence | 2% | stage ARR × 0.02 |
| Pipeline Management | 2% | stage ARR × 0.02 |
| Sales Enablement | 1.5% | stage ARR × 0.015 |

### The category → partner map (with disclosure, every time)

| Uncovered category | Partner | go/ slug |
|--------------------|---------|----------|
| CRM | Attio | `artemisgtm.ai` |
| Sales Engagement | Amplemarket | `artemisgtm.ai` |
| Data Enrichment | Amplemarket (bundled) · Apollo (standalone) | `artemisgtm.ai` · `artemisgtm.ai` |
| Visitor Identification | Warmly | `artemisgtm.ai` |
| Conversation Intelligence | Sybill (SMB–mid) · Attention (enterprise) | `artemisgtm.ai` · `artemisgtm.ai` |
| Revenue Intelligence / Sales Enablement | start with conversation intel (Attention / Sybill) | `artemisgtm.ai` · `artemisgtm.ai` |

---

## Section 5: Redundancies (the wasted spend)

Rank by annual waste, descending. One block per category carrying 3+ tools. This is a SEPARATE pool from the gap stake, duplicate spend, not forgone pipeline. Don't add it into the total-at-risk headline.

```
⚠️ REDUNDANCY #1 · [Category] $[Y]/year wasted −[Z] pts drag
───────────────────────────────────────────────────
Tools: [list the 3+ overlapping tools]
The math: $[stage ARR] × 0.4% × [extra tools] = ~$[Y]/year duplicate spend
The fix: Consolidate to 1-2. The Tech Stack Audit Agent sequences the
 migration so you cut the duplicate without breaking ops.
 Common moves: two enrichment tools → one; Gong → Sybill;
 Outreach → Amplemarket.
```

Repeat for every redundancy. State the points of drag it cost the score so the buyer sees consolidating recovers both the spend AND the grade.

---

## Section 6: The Consolidation Engagement (one paid agent, no bundle ladder)

This grade maps to exactly ONE paid module, there is no bundle ladder. State the return math from the buyer's own total exposure:

```
You've got [N] gaps worth $[X]/year forgone and [M] redundancies worth
$[Y]/year in duplicate spend. $[X+Y] of total exposure on the table.

The Tech Stack Audit module (modules/stack/) sequences the
whole fix: it pulls your real invoice ledger, scores every tool on
utilization, outcome, and overlap, fills the gaps in priority order, cancels
the duplicates, migrates without breaking GTM ops, and hands your CFO a
defensible savings summary.
The shell places it on your roadmap and quotes the price.

Recover even a tenth of $[X+Y] and the module pays for itself many times
over in year one.
```

Route the Tech Stack Audit module per the module-gate doctrine: log the gap, the shell places it on the roadmap and quotes the price. Never a prose price, a checkout link, or a codename.

---

## Section 7: The Roadmap (close)

Tell them exactly what to do first. Three parts, advisor voice:

```
WHAT TO FIX FIRST
The #1 gap is [category] at $[X]/year forgone, and at [weight] points it's
also the biggest single score move. Fill it with [partner]. The #1
redundancy is [category] at $[Y]/year wasted, cut it to one tool.

THE FULL SEQUENCE
1. Fill [Gap #1] → [partner] (week 1-2)
2. Cut [Redundancy #1] → consolidate (week 2-4)
3. Fill [Gap #2] → [partner] (week 4-6)
...or run the Tech Stack Audit Agent and we sequence all of it on one
timeline with a CFO-ready savings summary.

WHAT I'D WATCH
- [Category]: re-check at the quarterly re-grade; covering it adds [weight] pts.
- [Category]: watch for a third tool creeping in, that's new drag.
Baseline saved to coverage-score-ledger.md. Re-run this grade in 90 days
(quarterly-regrade routine) to track the Coverage Score trend and catch new
sprawl as you grow.
```

This grade is free, so there's no `SHIPPED` finisher's code to offer here (nothing was built). The reward path is the paid Tech Stack Audit Agent: run that build to completion and the `SHIPPED` discount is waiting on the next agent. Close by pointing to the one fix that matters most, not by dangling a code the buyer hasn't earned yet.

---

## Fill rules

- Never show a gap without a $ figure and a cost-of-delay.
- Never show a redundancy without the wasted-spend figure and the drag points.
- Keep gap stake (forgone pipeline) and redundancy waste (duplicate spend) as SEPARATE pools, never add them into one "at risk" total.
- Never recommend a partner without the per-category reason AND the disclosure.
- Only flag a gap for a category in the stage's must/should/nice map. No wrong-stage gaps, no phantom gaps.
- Count unlisted tools as coverage, don't invent a gap for a category the buyer covers with a tool you didn't recognize.
- Round dollars to two significant figures. No false precision.
- Every number traces to the buyer's stage and the shipped leak/waste rates.
