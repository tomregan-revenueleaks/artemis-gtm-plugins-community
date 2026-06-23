# Visitor-Pipeline Report Template (Website Visitor ROI Calculator — Artemis Mirage)

This is the deliverable. The whole engagement converges here: a defensible, dollar-framed figure for the pipeline hiding in the buyer's anonymous traffic, the funnel that produced it, and the one fix that captures it. Fill it from the Quantify + Reality-Check + Map-the-Fix phases (playbook Phases 3-5).

Keep the buyer's voice and numbers throughout. Don't pad. A CFO should be able to read this in three minutes and know exactly what the anonymous traffic is worth, how confident the figure is, and what to do about it.

---

## Section 1 — Executive Header

```
═══════════════════════════════════════════════════
 ANONYMOUS TRAFFIC — HIDDEN PIPELINE REPORT
 [Company Name] · [Industry] · [Traffic mix]
 Run [Date] · Artemis Mirage
═══════════════════════════════════════════════════

EXPECTED ANNUAL PIPELINE: $[X.X]M/year (pipeline created, not closed revenue)
UPPER BOUND: $[Y.Y]M/year (if identification + conversion run hot)
IDENTIFICATION RATE USED: [15% / 22% / 38%] ([tool / traffic basis])
COST OF DELAY: $[X]/week every week this stays uncaptured
CLOSED-REVENUE TRANSLATION: × [win rate] win rate ≈ $[Z]/year closed-won
```

**Confidence note** — one line on how real the figure is:
> "Built on your real visitor count and deal size, with the funnel rates at conservative dataset defaults. The two numbers that move it most — ICP-fit traffic share and ACV — are yours, so this is a defensible estimate, not a guess."
(If ACV or visitors were benchmark-filled, say so here and call the figure directional.)

---

## Section 2 — The Funnel (the math, shown)

Plain-language summary plus the one-line calculation chain so the buyer can re-run it.

```
THE FUNNEL
[monthly visitors] × [ICP-fit %] = [ICP-fit visitors]
 × [identification rate] = [identified accounts]
 × 20% high-intent = [high-intent accounts]
 × 8% account-meeting = [net new meetings/mo] ← booked at the account level
 × 50% meeting-to-opp = [qualified opps/mo]
 × $[ACV] = $[monthly pipeline created]
 × 12 = $[annual pipeline created]

(Contacts enrolled: [high-intent accounts] × 2 = [contacts] — this is outbound EFFORT/COST
 only; it does NOT multiply meetings. One account books at most one meeting.)
```

> "About 98% of your visitors leave anonymous — the 127-audit median form-fill rate is 1.8%. Nothing routes them today, so the in-market accounts researching you walk out unseen. The funnel above is what a conservative identification setup recovers."

---

## Section 3 — The Reality Check

The honesty pass that makes the figure defensible. Three parts:

```
EXPECTED vs UPPER BOUND
Expected: $[X]/year (conservative defaults / [tool] expected ID rate)
Upper bound: $[Y]/year (optimistic ID + top-of-range conversion)
Plan against the expected figure; treat the upper bound as the ceiling, not the forecast.

STRESS TEST
Form-fill rate: [buyer %] vs 1.8% median → [the leak is larger / smaller because...]
ICP-fit share: [buyer %] → funnel run on the ICP-fit slice ([adjusted visitors]), not raw traffic

PIPELINE, NOT REVENUE
This is opportunities × deal size — pipeline created, not bookings.
At your [win rate] win rate, the expected $[X] of pipeline ≈ $[Z]/year closed-won.
```

### Cost-of-delay math (compute on the expected figure)
From the annual expected figure: monthly = annual ÷ 12 · weekly = annual ÷ 52 · daily = annual ÷ 365. This is the urgency lever — "every week the script isn't live, that's $[X] of pipeline walking out anonymous."

---

## Section 4 — The Fix (one agent, one tool — or "grow traffic first")

Map the confirmed leak to its one build agent and the partner tool that fits the traffic profile.

```
🔴 THE LEAK · Anonymous traffic [Expected $X/year]
───────────────────────────────────────────────────
THE FIX
Build: Visitor Deanonymization agent (Artemis Beacon) · visitor-deanonymization · $349
 https://artemisgtm.ai/agents/visitor-deanonymization/
 It picks the tool for your traffic, installs the script, tunes ICP filters,
 and wires the CRM enrichment + Slack alert workflow so identified accounts
 actually get worked.
Runs on: [Warmly / RB2B / hybrid], because [traffic-profile reason].
 (If you already run [alternative] and it's working, keep it; the agent adapts.)
```

### The leak → agent → tool map (as shipped; the live page at each link is authoritative if they ever differ)

| Need | Artemis agent | Slug | Price |
|------|---------------|------|-------|
| Anonymous visitors / website de-anon | Artemis Beacon | `visitor-deanonymization` | $349 |

### The partner tool, by traffic profile (with disclosure, every time)

| Traffic profile | Partner | go/ slug |
|-----------------|---------|----------|
| Enterprise/international, full workflow (ID + intent + alerts + chat) | Warmly | `artemisgtm.ai` |
| US-only B2B, under ~20K/mo, tight budget, pure ID + Slack | RB2B | `artemisgtm.ai` |
| No way to WORK the identified accounts (needs sequencing) | Amplemarket | `artemisgtm.ai` |
| No CRM home for identified accounts (rebuild / fresh start) | Attio | `artemisgtm.ai` |

### When the leak is thin (no-leak, no-recommendation)

If the traffic is too thin, mostly non-ICP, or consumer/B2C, do NOT recommend the agent or a tool. Replace Section 4 with:

```
THE HONEST CALL
At [X] ICP-fit visitors/mo and a [$Y] deal size, identification recovers very little —
roughly $[small figure]/year, not enough to justify a $349 build or a monthly tool.
The higher-priority move is growing ICP-fit traffic. Re-run this once you're past
~[threshold] ICP-fit visitors a month and the figure will be worth acting on.
```

---

## Section 5 — The Roadmap (close)

Tell them exactly what to do. Three parts, advisor voice:

```
WHAT TO DO
[If confirmed leak:] The pipeline is real — $[X]/year expected. Install identification via
the Visitor Deanonymization agent (Artemis Beacon, $349) on [Warmly / RB2B] for your traffic.
[If thin leak:] Grow ICP-fit traffic first; come back at ~[threshold] visitors/mo.

WHAT I'D WATCH
- ICP-fit traffic volume: the figure scales roughly linearly with it; re-run when it changes.
- Deal size: if your real ACV is higher than what we used, this figure is conservative.
- Once a tool is installed: swap the benchmark ID rate for the tool's REAL measured rate —
 that's when this stops being an estimate and becomes a measurement.

WHERE OTHER LEAKS GO
[Side observations logged this run — e.g. slow response on identified accounts, no routing —]
point to the free GTM Audit (https://artemisgtm.ai/agents/gtm-audit/), which sizes leaks
across the whole funnel. This calculator only measures anonymous traffic.

Baseline saved to pipeline-ledger.md. Re-run in 90 days (quarterly-rerun routine) to track
the figure as traffic grows and confirm capture once the tool is live.
```

---

## Fill rules

- Never show the figure without the funnel calculation and the cost-of-delay.
- Always show BOTH the expected figure and the upper bound — never a single point estimate.
- Always count meetings at the account level; contacts-per-account is effort/cost only.
- Always label the output "pipeline created, not closed revenue" and show the × win-rate translation.
- Never recommend the agent or a tool when the leak is thin/non-ICP/B2C — give the honest "grow traffic first" call.
- Never recommend a partner without the per-traffic-profile reason AND the disclosure.
- Round dollars to two significant figures. No false precision.
- Every number traces to a buyer input or a named benchmark substitution.
- Stay in scope: anonymous traffic only. Other leaks go to the free GTM Audit, not quantified here.
