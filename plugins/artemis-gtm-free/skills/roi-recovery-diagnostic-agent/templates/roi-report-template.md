# Recoverable Revenue Report Template (Recoverable Revenue ROI — Artemis Ledger)

This is the deliverable. The whole engagement converges here: one defensible recoverable-revenue figure, the per-lever breakdown, the cycle-velocity line, the cost-of-delay, and the fix-map — the artifact the buyer screenshots and forwards to finance. Fill it from the Benchmark + Compute + Map steps (playbook Steps 2-7).

Keep the buyer's voice and numbers throughout. Don't pad. A CFO should be able to read this in three minutes and know exactly how much the funnel leaks, which lever leaks it, and what to do first.

---

## Section 1 — Executive Header

```
═══════════════════════════════════════════════════
 RECOVERABLE REVENUE — ROI DIAGNOSTIC
 [Company Name] · [Industry, if known] · B2B SaaS
 Run [Date] · Artemis Ledger
═══════════════════════════════════════════════════

RECOVERABLE REVENUE: $[X.X]M/year (optimized funnel − current funnel)
DEFENSIBLE (median): $[Y.Y]M/year (against the realistic median bar)
BIGGEST LEVER: [Lead-to-opp / Win rate] — $[Z]/year of the total
COST OF DELAY: $[X]/week every week this stays unfixed
```

**The headline is a stretch.** The recoverable figure uses the top-quartile bar (25% lead-to-opp, 50% win rate) — the upside *if* you reach top-quartile performance, not a guaranteed result. Always print the median figure beside it so the buyer has the conservative number to take to finance. Say which is which.

---

## Section 2 — The Diagnosis (one paragraph)

Plain-language summary of where the funnel leaks and why, in the advisor's voice. No jargon. Name the single biggest lever and what's driving it.

> "Your funnel sources leads fine; 200/month is healthy for your size. The leak is in conversion: 15% of leads become opps against a 25% top-quartile bar, and 35% of those close against 50%. Modeling both improvements in one optimized funnel — so we don't double-count the deals that flow through both stages — your recoverable revenue is $4.4M/year at top quartile, $1.2M against the realistic median. Lead-to-opp carries most of it."

---

## Section 3 — The Calculation (show your work)

The buyer trusts a number they can re-run. Print the chain on one line each.

```
THE CALCULATION
───────────────────────────────────────────────────
Current funnel: [leads] × 12 × [lto]% × [wr]% × $[deal] = $[current]/yr
Optimized funnel: [leads] × 12 × [b-lto]% × [b-wr]% × $[deal] = $[optimized]/yr
Recoverable: $[optimized] − $[current] = $[total]/yr (top-quartile stretch)
Defensible (median): same funnel at [m-lto]% / [m-wr]% = $[median]/yr
```

Round to two significant figures. "$4.4M/year," never "$4,350,000/year."

---

## Section 4 — Per-Lever Breakdown (reconciles to the total)

The two conversion levers, split proportionally so they sum exactly to the recoverable total. The larger names the fix.

```
PER-LEVER BREAKDOWN
───────────────────────────────────────────────────
Lead-to-opp conversion $[A]/year [buyer]% → [bench]% ([ratio])
Win rate $[B]/year [buyer]% → [bench]% ([ratio])
───────────────────────────────────────────────────
 $[total]/year (A + B reconciles to the recoverable total)
```

Note any lever the buyer already beats benchmark on as a STRENGTH, not a gap: "Your win rate is already at 48% against the 50% bar — that lever's contribution is ~$0, which is good news. The whole recoverable figure sits in lead-to-opp."

---

## Section 5 — Cycle Velocity (separate — NOT in the recoverable total)

Report cycle as the velocity benefit it is, visibly fenced off from recoverable revenue.

```
CYCLE VELOCITY (a velocity benefit, NOT added to the recoverable total above)
───────────────────────────────────────────────────
[daysFaster] days faster to close ([buyer]d → [bench 60]d)
≈ $[cycleVelocityValue] in cash pulled forward (one-time working-capital gain, not recurring ARR)
```

If the buyer's cycle already beats 60 days, `daysFaster` is 0 — say "your cycle is already at or under the 60-day benchmark; no velocity opportunity here," and skip the dollar line.

---

## Section 6 — Cost of Delay

From the recoverable total: monthly = annual ÷ 12 · weekly = annual ÷ 52 · daily = annual ÷ 365. This is the urgency lever.

```
COST OF DELAY
───────────────────────────────────────────────────
Annual: $[total]/yr
Monthly: $[monthly]/mo
Weekly: $[weekly]/wk
Daily: $[daily]/day
```

> "Every week this stays unfixed is roughly $[weekly] you don't recover. The diagnostic was free; the leak isn't."

---

## Section 7 — The Fix (no gap, no recommendation)

Map the single biggest lever to the agent that rebuilds it and the partner platform it runs on. One block.

```
THE FIX — biggest lever: [lever name] ($[Z]/year)
───────────────────────────────────────────────────
Build: [Artemis agent name] · [slug] · $349
 https://artemisgtm.ai/agents/[slug]/
Partner: [Tool], [why this tool for THIS lever].
 (If you already run [alternative] and it's working, keep it; the
 agent adapts to your stack.)
```

### The canonical lever → Artemis agent map (this model has two buyable levers; cycle is velocity, not buyable)

| Lever | Artemis agent | Slug | Price |
|-------|---------------|------|-------|
| Lead-to-opp conversion gap | Artemis Gateway | `qualification-automation-system` | $349 |
| Win-rate gap | Artemis Lander | `conversion-content-system` | $349 |
| Sales-cycle drag (velocity, context only) | — (no buyable fix on cycle alone) | — | — |

### The partner per lever (with disclosure, every time)

| Lever / need | Partner | go/ slug |
|--------------|---------|----------|
| Lead-to-opp: signal-based qualification + enrichment | Amplemarket | `artemisgtm.ai` |
| Lead-to-opp: the CRM layer for scoring + MQL-to-SQL handoff | Attio | `artemisgtm.ai` |
| Win rate: deal-risk + loss analysis (conversation intelligence) | Attention | `artemisgtm.ai` |

---

## Section 8 — The Bundle (only at 2 confirmed gaps; coverage framing, not savings)

This model usually confirms one lever; sometimes two. At exactly two gaps, both fixes live inside the GTM System bundle, but two singles ($698) are still cheaper than the bundle ($997), so the only honest framing is coverage — never savings.

```
Two levers are leaking: lead-to-opp ($[A]) and win rate ($[B]). Two singles
is $698 — Qualification Automation plus Conversion Content. The GTM System
("Artemis Constellation") is $997, so it costs $299 more than the pair, but
it includes the other five system agents (Content, Outbound, Nurture, ICP,
AI RevOps) for the parts of the funnel this ROI model doesn't see.
https://artemisgtm.ai/agents/complete-gtm-system/
```

Savings framing (showing the $1,446 saved vs $2,443 separate) only applies at 3+ confirmed gaps — which this model alone won't usually produce, since it has two buyable levers. Don't claim savings on a two-gap diagnostic.

---

## Section 9 — The Close

Tell them exactly what to do first. Three parts, advisor voice:

```
WHAT TO FIX FIRST
The biggest lever is [name] at $[Z]/year — [reason it's also the fastest /
highest-leverage]. The agent for it is [Artemis agent] ([slug], $349).

WHAT I'D WATCH
- [Lever]: re-run this in 90 days; target [benchmark]. As it climbs, your
 recoverable figure shrinks — that's the leak closing.
- [Cycle, if relevant]: [threshold].
Baseline saved to recoverable-ledger.md. Re-run this diagnostic in 90 days
(quarterly-rerun routine) to track the recoverable figure trending down.

WHAT THIS MODEL DOESN'T SEE
This ROI model only quantifies conversion, win rate, and cycle. If the real
leak is upstream (anonymous traffic, slow lead response, undefined ICP), the
full GTM Audit (Artemis Compass) finds it. Worth a run if your numbers here
came back clean but pipeline still feels thin.
```

---

## Fill rules

- Never show the recoverable figure without the calculation chain and the median version beside it.
- Never recommend an agent without the matching lever named and a non-zero dollar contribution.
- Never recommend a partner without the per-lever reason AND the disclosure.
- Never add cycle velocity into the recoverable total — it's always fenced off as a separate section.
- Never sum the per-lever standalone figures as the total — use the proportional split that reconciles.
- Round dollars to two significant figures. No false precision.
- Every number traces to a buyer input or a named benchmark substitution.
