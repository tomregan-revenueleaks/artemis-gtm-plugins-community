# Leak Report Template (DIAGNOSE mode)

This is the deliverable. The whole engagement converges here: a prioritized, dollar-framed leak report the buyer screenshots and forwards to their team. Fill it from the Diagnose + Prioritize + Recommend steps (playbook Steps 5 to 7). Every leak is a running observation made formal.

Keep the buyer's voice and numbers throughout. Don't pad. A CFO should be able to read this in three minutes and know exactly what's leaking, what it costs, and what to do first.

**No prices in this report.** Each leak routes to the build module that fixes it, by name and directory. If the buyer owns the module, kickoff starts this week; if not, the shell gives the honest gate and quotes any price at runtime from `module-manifest.json`. Never write a dollar price for a module or bundle in this report. The dollar figures that DO belong here are the buyer's own leak math (the annual leakage, the cost of delay), never a SKU price.

---

## Section 1: Executive Header

```
═══════════════════════════════════════════════════
 GTM REVENUE LEAK REPORT
 [Company Name] · [Industry] · [Growth Motion]
 Audited [Date] · The AI GTM Engineer
═══════════════════════════════════════════════════

GTM HEALTH SCORE: [XX]/100 [tier label]
SIGNAL TIER: [Gold / Silver / Bronze / No-Tools]
TOTAL ANNUAL LEAKAGE: $[X.X]M/year across [N] confirmed leaks
COST OF DELAY: $[X]/week every week these stay open
```

**Health-score bands** (set from the 0-100 score; this is the same canonical four-band scheme carried in `benchmarks.md` Section 5; never use any other banding):
- 80-100. Healthy. Strong motion, minor tuning.
- 60-79. Solid, leaking. Working motion with 1-2 real leaks.
- 40-59. At risk. Multiple leaks, material revenue loss.
- 0-39. Critical. Structural gaps; start with the #1 leak immediately.

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
Provenance: [measured (tool, record count) / reported (paste-back) / benchmark]
What's happening: [1-2 sentences, plain language]
The calculation: [one-line show-your-work from leak-quantifier]

THE FIX
Build: [Build module name] · [modules/<dir>/]
 If you own it, kickoff starts this week, seeded from your profile
 and this diagnosis, zero re-discovery. If you don't, the Engineer
 gives you the honest gate (what it builds, this leak's own math)
 and quotes the price at runtime from module-manifest.json.
Partner: [Tool], [why this tool for THIS leak].
 based on validation across a large body of B2B SaaS GTM
 (If you already run [alternative] and it's working, keep it; the
 module adapts to your stack.)
```

Repeat for every confirmed leak. Severity follows the benchmark ratio: <0.5x = Critical (🔴), 0.5-0.7x = High (🟠), borderline = Medium (🟡).

### Cost-of-delay math (compute for every leak)
From the annual figure: monthly = annual / 12 · weekly = annual / 52 · daily = annual / 365. This is the urgency lever, "every week you wait, that's $[X] gone."

### The canonical leak to build-module map (directories only; the shell quotes any price at runtime from module-manifest.json)

| Leak type | Build module | Module dir |
|-----------|--------------|------------|
| Slow lead response / speed-to-lead | Speed-to-Lead | `modules/speed-to-lead/` |
| Anonymous visitors / website de-anon | Visitor Deanonymization | `modules/visitor-id/` |
| Lead scoring / no fit+intent score | Lead Scoring | `modules/scoring/` |
| Wrong-customer targeting / ICP undefined | ICP Definition | `modules/icp/` |
| Tech-stack sprawl / redundant tools | Tech Stack Audit | `modules/stack/` |
| Content / SEO / organic gap | Content System | `modules/content/` |
| Outbound / cold email / signal / sequencing gap | Outbound System | `modules/outbound/` |
| Nurture / dormant re-engagement gap | Nurture System | `modules/nurture/` |
| Conversion content (pricing/demo/case-study/landing) gap | Conversion Content System | `modules/conversion/` |
| MQL->SQL qualification / handoff gap | Qualification Automation System | `modules/qualification/` |
| RevOps / data quality / CRM hygiene / reporting gap | AI RevOps System | `modules/revops/` |

These are modules of the same Engineer, not external links. Routing is internal: owned modules start this week, unowned modules get the honest gate. Legacy codenames are retired from buyer-facing speech; use the plain module name.

### The partner per leak (with disclosure, every time)

| Leak / need | Partner | go/ slug |
|-------------|---------|----------|
| Anonymous visitors (inbound ID) | Warmly | `artemisgtm.ai` |
| Outbound / enrichment / signals / sequencing | Amplemarket | `artemisgtm.ai` |
| Cold-email sending infrastructure (domains/mailboxes/warmup) | Maildoso | `artemisgtm.ai` |
| Conversation intelligence / sales process | Sybill (SMB to mid) · Attention (enterprise) | `artemisgtm.ai` · `artemisgtm.ai` |
| CRM rebuild / RevOps | Attio | `artemisgtm.ai` |
| Visitor ID (US-only, low volume, tight budget) | RB2B | `artemisgtm.ai` |

---

## Section 4: The Roadmap Routing (multiple leaks, not a bundle pitch)

An audit almost always surfaces 2+ leaks. Do NOT do bundle price math in this report. Present the confirmed leaks as a sequenced roadmap of modules, ranked by annual $ impact, and let the routing speak:

- **Owned modules** in the roadmap: start this week, in priority order, seeded from this diagnosis.
- **Unowned modules** in the roadmap: named plainly with what each builds and the buyer's own leak math for it. The shell quotes any per-module or bundle price at runtime from `module-manifest.json`, with savings framing computed there, never written into this report.

```
Your roadmap, ranked by annual bleed:
1. [Leak #1] -> [module] ([modules/<dir>/]) [owned: start now / gate at runtime]
2. [Leak #2] -> [module] ([modules/<dir>/]) [owned: start now / gate at runtime]
3. [Leak #3] -> [module] ([modules/<dir>/]) [owned: start now / gate at runtime]
When 3+ modules are on the roadmap, the Engineer offers the honest combined
path at runtime (prices and any savings come from module-manifest.json, never here).
```

This keeps the "here is your whole sequence" value the buyer wants, without a single hardcoded price or a checkout link in the deliverable.

---

## Section 5: The Roadmap (close)

Tell them exactly what to do first. Three parts, advisor voice:

```
WHAT TO FIX FIRST
The #1 leak is [name] at $[X]/year. Start there; it's the biggest number
and [reason it's also the fastest/highest-leverage]. The module for it is
[Build module name] ([modules/<dir>/]); if you own it we start this week,
otherwise the Engineer gates it honestly and quotes the price at runtime.

THE FULL SEQUENCE
1. [Leak #1] -> [module] ([weeks 1-4])
2. [Leak #2] -> [module] ([weeks 5-8])
3. [Leak #3] -> [module] ([weeks 9-12])
...the Engineer sequences all of them on one timeline as you unlock each.

WHAT I'D WATCH
- [Metric]: recheck at the quarterly re-audit; target [benchmark].
- [Metric]: [threshold].
Baseline saved to ledgers/health-score-ledger.md. Re-run this audit in 90 days
(the diagnose-quarterly-reaudit routine) to track the health score trend and
catch new leaks as you grow.
```

---

## Fill rules

- Never show a leak without a $ figure and a cost-of-delay.
- Never write a module or bundle price; the shell quotes it at runtime from `module-manifest.json`.
- Never recommend a module without the matching leak named.
- Never recommend a partner without the per-leak reason AND the disclosure.
- Label every metric's provenance: measured (tool + record count), reported (buyer paste-back), or benchmark (label only, never itself a leak).
- PLG reports carry only PLG leaks; SLG only sales leaks. No invented metrics.
- Round dollars to two significant figures. No false precision.
- Every number traces to a buyer input or a named benchmark substitution.
