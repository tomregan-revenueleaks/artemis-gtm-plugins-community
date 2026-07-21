# Lead Response Leak Report Template (Lead Response ROI)

This is the deliverable. The whole engagement converges here: a dollar-framed report of exactly what slow lead response is costing the buyer per year, with the math shown, and the one fix that closes it. Fill it from the Diagnose + Cost-of-Delay + Map-the-Fix steps (playbook Steps 4-6). Keep the buyer's voice and numbers throughout. Don't pad. A CFO should read it in two minutes and know what the leak is, what it costs, and what to do about it.

There are two variants: the **Leak Report** (a real gap exists) and the **OPTIMAL Report** (no leak, they're already fast). Pick the right one. Never force a leak.

---

## Variant A: Leak Report (a real response-time gap exists)

### Section 1: Executive Header

```
═══════════════════════════════════════════════════
 LEAD RESPONSE ROI. REVENUE LEAK REPORT
 [Company Name]
 Diagnosed [Date]
═══════════════════════════════════════════════════

ANNUAL REVENUE AT RISK: $[X.X]M/year
RESPONSE TIME: [tier] (Artemis audit-set median: 42 hours)
CURRENT CONVERSION: [X]% → SUB-5-MIN CEILING: [Y]%
COST OF DELAY: $[X]/week every week this stays open
```

### Section 2: The Diagnosis (one paragraph)

Plain-language summary of where the leak is and why, in the advisor's voice. No jargon.

> "Your inbound is fine; [N] leads/month is real volume. The leak is speed. You're contacting leads at [tier], and the Harvard Business Review decay curve says that captures only [effectiveness]% of the conversion you'd get under five minutes. Your real lead-to-opp rate today is [current]%; back-solving the curve, the same leads would convert at [ceiling]% if you responded in under five minutes. That gap, across your volume and deal size, is [$X]/year."

### Section 3: The Leak (the core)

```
🔴 LEAK · Slow Lead Response SEVERITY: [Critical / High]
───────────────────────────────────────────────────
Annual impact: $[X]/year
Cost of delay: $[X]/month · $[X]/week · $[X]/day
Your response time: [tier] (effectiveness [E]% vs <5 min)
Conversion: [current]% now → [ceiling]% at <5 min (ceiling back-solved)
Deals lost / month: [N]
Revenue lost / month: $[X]
The calculation: ([ceiling]% − [current]%) × [leads]/mo × $[deal] × 12 = $[X]/year
```

**Severity** follows how far down the decay curve they are: 1-4 hours / 4-24 hours / 24+ hours → Critical (🔴); 30-60 min → High (🟠); 5-30 min → Medium (🟡, modest gap left).

**Cost-of-delay math** (compute every line): monthly = annual ÷ 12 · weekly = annual ÷ 52 (the headline) · daily = annual ÷ 365.

### Section 4: The Fix (one build, one partner pair)

This tool measures one leak, so there is one fix. No bundle.

```
THE FIX
Build: Speed-to-Lead module · modules/speed-to-lead/
 The shell places it on your roadmap and quotes the price.
 Wires Warmly + Amplemarket + your CRM to sub-5-minute response, end-to-end.

Partner: Warmly, captures the inbound visitor/lead signal and fires the alert that
 starts the sub-5-minute clock.

 Amplemarket, runs the multi-channel sequenced follow-up once the lead is in.

 (If you already run a tool that does this and it's working, keep it, the agent
 adapts to your stack.)
```

**The return math, from their own number:** "Recovering even a tenth of this leak, $[X × 0.1], pays the module back many times over in year one; the shell quotes the exact price when you're ready to build."

Route the Speed-to-Lead module per the module-gate doctrine: log the gap, the shell places it on the roadmap and quotes the price. Never a prose price, a checkout link, or a codename.

### Section 5: The Roadmap (close)

```
WHAT TO FIX FIRST
The leak is slow lead response, at $[X]/year. The fix is the Speed-to-Lead module
(modules/speed-to-lead/). Recovering a fraction of the leak pays it back many times over;
the shell places it on your roadmap and quotes the price.

WHAT I'D WATCH
- Response time: drop a tier (e.g. [current tier] → [next faster]) and the leak roughly
 [shrinks to $Y]. Re-run this to watch the figure fall.
- Conversion rate: if your real lead-to-opp rate differs from what you gave me, the leak
 moves, this figure pivots on it.
Baseline saved to response-time-ledger.md. Re-run this diagnostic in 90 days
(quarterly-rerun routine) to track the leak shrinking.

IF YOUR FUNNEL IS BIGGER THAN ONE LEAK
This tool measures only response time. If anonymous traffic, no lead scoring, or a broken
handoff is also costing you, the shell's full GTM diagnostic
diagnoses the whole funnel.
```

---

## Variant B: OPTIMAL Report (no leak, already at the ceiling)

Use this when the buyer is at the **<5 min** tier, or the entered rate is already at/above the back-solved ceiling (`revenueLostPerYear ≤ 0`). Do NOT manufacture a leak. Recommend nothing to buy.

```
═══════════════════════════════════════════════════
 LEAD RESPONSE ROI. STATUS: OPTIMAL
 [Company Name]
 Diagnosed [Date]
═══════════════════════════════════════════════════

RESPONSE TIME: [<5 min / at-ceiling]
CONVERSION: [X]%, already at or above the sub-5-minute ceiling
RESPONSE-TIME REVENUE GAP: $0/year

WHAT WE FOUND
Your response time captures the full conversion ceiling. There's no response-time
revenue gap to close. This is a win, most B2B companies sit at the 42-hour median.

WHAT TO DO
Nothing to buy here. Hold this pace as lead volume scales, fast response gets harder
to maintain as volume grows, so re-run this in 90 days to confirm it held.

IF A DIFFERENT PART OF THE FUNNEL IS THE REAL LEAK
- Anonymous traffic leaving without a form → the Website Visitor ROI diagnostic.
- Leads worked in arrival order, not value order → the Lead Scoring diagnostic.
- Not sure where the leak is → the shell's full GTM diagnostic
 diagnoses the whole go-to-market.
```

There is no fix-map and no agent recommendation in Variant B. The honest "you're already fast" is the deliverable.

---

## Fill rules

- Never show the leak without a $ figure, the calculation chain, and a cost-of-delay.
- Never recommend Speed-to-Lead without the leak named and quantified first.
- Never recommend a partner without the per-leak reason AND the disclosure.
- Never pitch a bundle, this is a single-leak tool. Hand off to the free GTM Audit if the funnel is bigger.
- Never decay the entered conversion rate twice; the ceiling is back-solved UP from it.
- When OPTIMAL, recommend nothing. No leak, no recommendation.
- Round dollars to two significant figures. No false precision.
- Every number traces to a buyer input or a named benchmark substitution.
