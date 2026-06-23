# Leak Report Template (GTM Audit — Artemis Compass)

This is the deliverable. The whole engagement converges here: a prioritized, dollar-framed leak report the buyer screenshots and forwards to their team. Fill it from the Diagnose + Prioritize + Recommend steps (playbook Steps 5–7). Every leak is a running observation made formal.

Keep the buyer's voice and numbers throughout. Don't pad. A CFO should be able to read this in three minutes and know exactly what's leaking, what it costs, and what to do first.

---

## Section 1 — Executive Header

```
═══════════════════════════════════════════════════
 GTM REVENUE LEAK REPORT
 [Company Name] · [Industry] · [Growth Motion]
 Audited [Date] · Artemis Compass
═══════════════════════════════════════════════════

GTM HEALTH SCORE: [XX]/100 [tier label]
SIGNAL TIER: [Gold / Silver / Bronze / No-Tools]
TOTAL ANNUAL LEAKAGE: $[X.X]M/year across [N] confirmed leaks
COST OF DELAY: $[X]/week every week these stay open
```

**Health-score bands** (set from the 0–100 score — this is the same canonical four-band scheme as playbook Step 5.5; never use any other banding):
- 80–100 — Healthy. Strong motion, minor tuning.
- 60–79 — Solid, leaking. Working motion with 1-2 real leaks.
- 40–59 — At risk. Multiple leaks, material revenue loss.
- 0–39 — Critical. Structural gaps; start with the #1 leak immediately.

**Signal-tier line** — one sentence on what their intent/visitor stack can and can't do:
> "Silver tier: solid data layer (ZoomInfo), but no real-time activation; you know who your accounts are, you don't know when they're in-market."

---

## Section 2 — The Diagnosis (one paragraph)

Plain-language summary of where the GTM is leaking and why, in the advisor's voice. No jargon. Name the single biggest problem and the through-line connecting the leaks.

> "Your funnel sources leads fine; 300/month is right at benchmark for SaaS your size. The leak is downstream: leads sit before first touch, and the ones that do get worked convert at 9% to opp against a 15% benchmark. You're not short on top-of-funnel; you're losing the middle. Two fixes recover most of it."

---

## Section 3 — Ranked Leaks (the core)

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
Build: [Artemis agent name] · [slug] · $349
 https://artemisgtm.ai/agents/[slug]/
Partner: [Tool], [why this tool for THIS leak].
 (If you already run [alternative] and it's working, keep it; the
 agent adapts to your stack.)
```

Repeat for every confirmed leak. Severity follows the benchmark ratio: <0.5x = Critical (🔴), 0.5–0.7x = High (🟠), borderline = Medium (🟡).

### Cost-of-delay math (compute for every leak)
From the annual figure: monthly = annual ÷ 12 · weekly = annual ÷ 52 · daily = annual ÷ 365. This is the urgency lever — "every week you wait, that's $[X] gone."

### The canonical leak → Artemis agent map (slugs and prices as shipped; the live page at each link is authoritative if they ever differ)

| Leak type | Artemis agent | Slug | Price |
|-----------|---------------|------|-------|
| Slow lead response / speed-to-lead | Artemis Ignition | `speed-to-lead` | $349 |
| Anonymous visitors / website de-anon | Artemis Beacon | `visitor-deanonymization` | $349 |
| Lead scoring / no fit+intent score | Artemis Vector | `lead-scoring` | $349 |
| Wrong-customer targeting / ICP undefined | Artemis Trajectory | `icp-definition` | $349 |
| Tech-stack sprawl / redundant tools | Artemis Telemetry | `tech-stack-audit` | $349 |
| Content / SEO / organic gap | Artemis Halo | `content-system` | $349 |
| Outbound / cold email / signal / sequencing gap | Artemis Hunter | `outbound-system` | $349 |
| Nurture / dormant re-engagement gap | Artemis Harvester | `nurture-system` | $349 |
| Conversion content (pricing/demo/case-study/landing) gap | Artemis Lander | `conversion-content-system` | $349 |
| MQL→SQL qualification / handoff gap | Artemis Gateway | `qualification-automation-system` | $349 |
| RevOps / data quality / CRM hygiene / reporting gap | Artemis Mission Control | `ai-revops-system` | $349 |

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

## Section 4 — The Bundle Recommendation (savings framing only at 3+ leaks)

An audit almost always surfaces 2+ leaks. When you've confirmed **3 or more**, ladder to a bundle — at that count, buying the agents separately costs more than the bundle. At exactly 2 leaks, two singles ($698) are cheaper than either bundle, so the only honest framing is coverage ("the bundle includes the agents for the leaks you haven't hit yet"), never savings.

**If the leaks sit in the tactical set** (speed-to-lead, visitor de-anon, lead-scoring, icp-definition):
```
You've got [N] leaks worth $[X] combined, and they map to the tactical
agents. Bought separately that's $[N × 349]. The Launch Trio ("Artemis
Liftoff") is $799 for all four included agents: the three tactical
builds plus ICP Definition, $1,396 bought separately, so it saves $597.
https://artemisgtm.ai/agents/launch-trio/
```

**If the leaks span the systems** (content / outbound / nurture / conversion / qualification / RevOps):
```
You've got [N] leaks worth $[X] combined. Buying the [N] matching agents
separately is $[N × 349]. The GTM System ("Artemis Constellation") is
all seven agents (ICP Definition plus the six systems) for $997, $1,446
less than the $2,443 they cost separately. Because the audit found leaks
across [list the systems], the bundle is the cheaper and more complete
move than picking them off one at a time.
https://artemisgtm.ai/agents/complete-gtm-system/
```

Always show the separate-agents math next to the bundle price. The save is the argument — which is exactly why it's only made at 3+ confirmed leaks, where the arithmetic actually holds.

---

## Section 5 — The Roadmap (close)

Tell them exactly what to do first. Three parts, advisor voice:

```
WHAT TO FIX FIRST
The #1 leak is [name] at $[X]/year. Start there; it's the biggest number
and [reason it's also the fastest/highest-leverage]. The agent for it is
[Artemis agent] ([slug], $349).

THE FULL SEQUENCE
1. [Leak #1] → [agent] ([weeks 1–4])
2. [Leak #2] → [agent] ([weeks 5–8])
3. [Leak #3] → [agent] ([weeks 9–12])
...or take the [bundle] and we fix all of them on one timeline.

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
- Never recommend an agent without the matching leak named.
- Never recommend a partner without the per-leak reason AND the disclosure.
- PLG reports carry only PLG leaks; SLG only sales leaks. No invented metrics.
- Round dollars to two significant figures. No false precision.
- Every number traces to a buyer input or a named benchmark substitution.
