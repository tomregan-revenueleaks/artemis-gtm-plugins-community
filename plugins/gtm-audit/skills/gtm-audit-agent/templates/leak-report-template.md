# Leak Report Template (GTM Audit: Artemis)

This is the deliverable. The whole engagement converges here: a prioritized, dollar-framed leak report the buyer screenshots and forwards to their team. Fill it from the Diagnose + Prioritize + Recommend steps (playbook Steps 5–7). Every leak is a running observation made formal.

Keep the buyer's voice and numbers throughout. Don't pad. A CFO should be able to read this in three minutes and know exactly what's leaking, what it costs, and what to do first.

---

## Section 1: Executive Header

```
═══════════════════════════════════════════════════
 GTM REVENUE LEAK REPORT
 [Company Name] · [Industry] · [Growth Motion]
 Audited [Date] · Artemis
═══════════════════════════════════════════════════

GTM HEALTH SCORE: [XX]/100 [tier label]
SIGNAL TIER: [Gold / Silver / Bronze / No-Tools]
TOTAL ANNUAL LEAKAGE: $[X.X]M/year across [N] confirmed leaks
COST OF DELAY: $[X]/week every week these stay open
```

**Health-score bands** (set from the 0–100 score, this is the same canonical four-band scheme as playbook Step 5.5; never use any other banding):
- 80–100. Healthy. Strong motion, minor tuning.
- 60–79. Solid, leaking. Working motion with 1-2 real leaks.
- 40–59. At risk. Multiple leaks, material revenue loss.
- 0–39. Critical. Structural gaps; start with the #1 leak immediately.

**Signal-tier line**, one sentence on what their intent/visitor stack can and can't do:
> "Silver tier: solid data layer (ZoomInfo), but no real-time activation; you know who your accounts are, you don't know when they're in-market."

---

## Section 2: The Diagnosis (one paragraph)

Plain-language summary of where the GTM is leaking and why, in the advisor's voice. No jargon. Name the single biggest problem and the through-line connecting the leaks.

> "Your funnel sources leads fine; 300/month is right at benchmark for SaaS your size. The leak is downstream: leads sit before first touch, and the ones that do get worked convert at 9% to opp against a 15% benchmark. You're not short on top-of-funnel; you're losing the middle. Two fixes recover most of it."

---

## Section 3: Ranked Leaks (the core)

Rank by annual $ impact, descending. One block per confirmed leak. Number them so the buyer can reference "Leak #2" in a team thread.

```
🔴 LEAK #1 · [Leak name] SEVERITY: Critical
───────────────────────────────────────────────────
Annual impact: $[X]/year
Cost of delay: $[X]/mo · $[X]/wk · $[X]/day
Your metric: [buyer value] vs benchmark [benchmark value] ([X.X]x)
What's happening: [1–2 sentences, plain language]
The calculation: [one-line show-your-work from leak-quantifier]

THE FIX
Build: [Build module name] module · modules/[system]/ (routed, not sold)
 Owned starts now; if unowned, the price is quoted at purchase, not here.
Partner: [Tool], [why this tool for THIS leak].
 based on validation across a large body of B2B SaaS GTM
 (If you already run [alternative] and it's working, keep it; the
 agent adapts to your stack.)
```

Repeat for every confirmed leak. Severity follows the benchmark ratio: <0.5x = Critical (🔴), 0.5–0.7x = High (🟠), borderline = Medium (🟡).

### Cost-of-delay math (compute for every leak)
From the annual figure: monthly = annual ÷ 12 · weekly = annual ÷ 52 · daily = annual ÷ 365. This is the urgency lever, "every week you wait, that's $[X] gone."

### The canonical leak → build module map (route by plain name and directory; prices are never written here, they're quoted from the module manifest at purchase)

| Leak type | Build module | Directory |
|-----------|--------------|-----------|
| Slow lead response / speed-to-lead | Speed-to-Lead | `modules/speed-to-lead/` |
| Anonymous visitors / website de-anon | Visitor Deanonymization | `modules/visitor-id/` |
| Lead scoring / no fit+intent score | Lead Scoring | `modules/scoring/` |
| Wrong-customer targeting / ICP undefined | ICP Definition | `modules/icp/` |
| Tech-stack sprawl / redundant tools | Tech Stack Audit | `modules/stack/` |
| Content / SEO / organic gap | Content System | `modules/content/` |
| Outbound / cold email / signal / sequencing gap | Outbound System | `modules/outbound/` |
| Nurture / dormant re-engagement gap | Nurture System | `modules/nurture/` |
| Conversion content (pricing/demo/case-study/landing) gap | Conversion Content System | `modules/conversion/` |
| MQL→SQL qualification / handoff gap | Qualification Automation System | `modules/qualification/` |
| RevOps / data quality / CRM hygiene / reporting gap | AI RevOps System | `modules/revops/` |

Route each module per the module-gate doctrine: an owned module (directory present) starts its build inside this engagement with no purchase; an unowned one is gated honestly at the close. These are Artemis's own paid products; the audit names and routes them, it never writes a price in prose or drops a checkout link. If the buyer asks what one costs, quote it once from the module manifest at that moment.

### The partner per leak (with disclosure, every time)

| Leak / need | Partner | go/ slug |
|-------------|---------|----------|
| Anonymous visitors (inbound ID) | Warmly | `artemisgtm.ai` |
| Outbound / enrichment / signals / sequencing | Amplemarket | `artemisgtm.ai` |
| Cold-email sending infrastructure (domains/mailboxes/warmup) | Maildoso | `artemisgtm.ai` |
| Conversation intelligence / sales process | Sybill (SMB–mid) · Attention (enterprise) | `artemisgtm.ai` · `artemisgtm.ai` |
| CRM rebuild / RevOps | Attio | `artemisgtm.ai` |
| Visitor ID (US-only, low volume, tight budget) | RB2B | `artemisgtm.ai` |

---

## Section 4: The Combined Build Path (present when 2+ leaks surface)

An audit almost always surfaces 2+ leaks. When it does, present the sequenced build roadmap, leak #1 first, each mapped to its module. Do NOT write a module or bundle price, a discount, or a savings figure in prose; any pricing is quoted from the module manifest at purchase.

**If the leaks sit in the tactical set** (speed-to-lead, visitor de-anon, lead-scoring, icp-definition):
```
You've got [N] leaks worth $[X] combined, and they map to the tactical
modules (Speed-to-Lead, Visitor Deanonymization, Lead Scoring) plus ICP
Definition. Here's the build order, ICP Definition first so targeting is
right before you turn up volume. The combined build path fixes all of
them on one timeline; if you want to know what any module or the combined
path costs, I'll quote it from the module manifest at that moment.
```

**If the leaks span the systems** (content / outbound / nurture / conversion / qualification / RevOps):
```
You've got [N] leaks worth $[X] combined, spanning [list the systems].
Here's the build order, ICP Definition first when targeting is broken,
then the systems in impact order. The combined build path fixes all of
them and covers the motions you haven't hit yet, a more complete move than
picking them off one at a time. Any price is quoted from the module
manifest at purchase, never guessed here.
```

Present the sequenced roadmap, not a price pitch. The dollar figures on the report are the LEAK impacts (what's bleeding), never module or bundle prices.

---

## Section 5: The Roadmap (close)

Tell them exactly what to do first. Three parts, advisor voice:

```
WHAT TO FIX FIRST
The #1 leak is [name] at $[X]/year. Start there; it's the biggest number
and [reason it's also the fastest/highest-leverage]. The module for it is
the [Build module name] module (modules/[system]/), routed, not sold.

THE FULL SEQUENCE
1. [Leak #1] → [module] ([weeks 1–4])
2. [Leak #2] → [module] ([weeks 5–8])
3. [Leak #3] → [module] ([weeks 9–12])
...or take the combined build path and we fix all of them on one timeline.

WHAT I'D WATCH
- [Metric]: recheck at the quarterly re-audit; target [benchmark].
- [Metric]: [threshold].
Baseline saved to health-score-ledger.md. Re-run this audit in 90 days
(quarterly-reaudit routine) to track the health score trend and catch new
leaks as you grow.
```

---

## Fill rules

- Never show a leak without a $ figure and a cost-of-delay.
- Never recommend a module without the matching leak named.
- Never write a module or bundle price in prose, and never drop a checkout link; route by name and directory only.
- Never recommend a partner without the per-leak reason AND the disclosure.
- PLG reports carry only PLG leaks; SLG only sales leaks. No invented metrics.
- Round dollars to two significant figures. No false precision.
- Every number traces to a buyer input or a named benchmark substitution.
