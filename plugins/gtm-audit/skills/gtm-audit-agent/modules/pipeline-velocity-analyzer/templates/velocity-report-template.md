# Velocity Report Template (Pipeline Velocity)

This is the deliverable. The whole engagement converges here: a velocity read the buyer screenshots and forwards to their VP of Sales or board. Fill it from the Compute + Benchmark + Lever + Roadmap phases (playbook Phases 3-6). Every lever is a running observation made formal.

Keep the buyer's voice and numbers throughout. Don't pad. A VP of Sales should be able to read this in two minutes and know exactly how fast their pipeline moves, what the gap costs, and which lever to pull first.

---

## Section 1: Executive Header

```
═══════════════════════════════════════════════════
 PIPELINE VELOCITY READ
 [Company Name] · [Industry] · [Growth Motion]
 Computed [Date]
═══════════════════════════════════════════════════

PIPELINE VELOCITY: $[X]/day (Grade [A-F], [label])
ANNUAL PIPELINE REVENUE: $[X.X]M/year
INDUSTRY BENCHMARK: $[X]/day for [industry] × [motion] ([ratio]x)
VELOCITY LEAK vs BENCHMARK: $[X]/year (recoverable)
COST OF DELAY: $[X]/week every week the winning lever sits unpulled
```

**Grade bands** (set from the velocity ratio, same scale as the Pipeline Velocity Calculator; never use any other banding):
- ≥ 1.5. A, Exceptional. Velocity well above peers.
- ≥ 1.15. B, Above benchmark. Healthy; protect it.
- ≥ 0.85. C, At benchmark. Fine; tune the weakest lever.
- ≥ 0.6. D, Below benchmark. Leaking; pull a lever.
- < 0.6. F, Critical. Structural velocity gap.

**If at/above benchmark (no leak):** drop the "velocity leak" and "cost of delay" lines, replace with "At/above benchmark, no recoverable leak. Strongest lever to protect: [lever]." Do not manufacture a leak.

---

## Section 2: The Read (one paragraph)

Only fill this section after the buyer has confirmed the Phase 3 read-back (the opportunities/mo, deals/mo, and $/day velocity). The benchmark and grade are computed off a number the buyer signed off on, not off raw recall. If they flagged the deals-per-month figure as off, fix the units upstream and recompute before this section gets written.

Plain-language summary of how fast the pipeline moves and where the constraint is, in the advisor's voice. No jargon. Name the velocity, the benchmark gap, and the single lever to pull.

> "Your pipeline moves at $3,667/day, a D against the $5,400/day SaaS benchmark, a $720K/year gap. The math is honest: 200 leads convert to 30 real opportunities, and those 30 close at an 18% win rate that's 30% under the 25% benchmark. You're not short on top-of-funnel; the leak is conversion. Pull win rate and most of the gap closes."

Show the velocity math in one line right under the read:

> Opportunities = 200 × 15% = 30/mo. Velocity = (30 × $25,000 × 18%) / 45 = **$3,000/day**.

---

## Section 3: The Winning Lever (the core)

```
THE WINNING LEVER · [Lever name] +$[X]/day (~$[X]/year)
───────────────────────────────────────────────────
$/day impact: +$[X]/day
Annual impact: ~$[X]/year of added throughput
Your input: [buyer value] vs benchmark [benchmark value] ([X.X]x)
Why this lever: [benchmark-gap reasoning, where you're furthest below par]
The math: [current → improved = new velocity, one line]

THE FIX
Build: [Artemis module name] · modules/[slug]/
 The shell places it on your roadmap and quotes the price.
Partner: [Tool], [why this tool for THIS lever].
 (If you already run [alternative] and it's working, keep it; the
 agent adapts to your stack.)
```

### The other three levers (ranked, for context)

```
2. [lever] +$[X]/day (~$[X]/year) [why it's not the pull, at benchmark, or smaller gap]
3. [lever] +$[X]/day (~$[X]/year)
4. [lever] +$[X]/day (~$[X]/year)
```

Explain the flat-25% nuance once so the ranking is trusted: "On a flat 25%, the three numerator levers add about the same and cycle shows the biggest raw delta because it's the denominator. I ranked by where you're furthest below benchmark, because that's the lever with real, recoverable room."

### Cost-of-delay math (compute on the velocity leak)
From the annual leak: monthly = annual ÷ 12 · weekly = annual ÷ 52 · daily = annual ÷ 365. This is the urgency lever, "every week the winning lever sits unpulled, that's $[X] of velocity gone."

### The canonical lever → module map (as shipped)

| Winning lever | Root cause | Artemis module | Module path |
|---------------|-----------|----------------|-------------|
| Higher Win Rate | Decision-stage content doesn't close | Conversion Content System | `modules/conversion/` |
| Bigger Deals | Pricing/ROI pages don't anchor value | Conversion Content System | `modules/conversion/` |
| Bigger Deals (targeting root cause) | Accounts are structurally too small | ICP Definition | `modules/icp/` |
| More Leads (outbound) | Not enough top-of-funnel | Outbound System | `modules/outbound/` |
| More Leads (anonymous traffic) | In-market visitors never identified | Visitor Deanonymization | `modules/visitor-id/` |
| Shorter Cycles | Slow first-touch and stage progression | Speed-to-Lead | `modules/speed-to-lead/` |

Route every module per the module-gate doctrine: log the gap, the shell places it on the roadmap and quotes the price. Never a prose price, a checkout link, or a codename. The Conversion Content System is the primary, most common pairing because it moves the two levers, win rate and deal size, that most often leak.

### The partner per lever (with disclosure, every time)

| Winning lever | Partner | go/ slug |
|---------------|---------|----------|
| Higher Win Rate / Bigger Deals | Attention (conversation intelligence, deal coaching) | `artemisgtm.ai` |
| More Leads (outbound) | Amplemarket (signal-based sequencing + enrichment) | `artemisgtm.ai` |
| More Leads (inbound) / Shorter Cycles | Warmly (visitor ID + real-time intent triggers) | `artemisgtm.ai` |

---

## Section 4: A Second Lever or Bundle (only if a second lever genuinely leaks)

A velocity read produces ONE primary recommendation. Only add this section if a SECOND lever is also a confirmed below-benchmark leak with a real dollar figure.

**Two confirmed lever leaks**:
```
Two of your four levers are leaking, [lever A] and [lever B], worth
$[X] combined per year. Each maps to a build module ([module A],
[module B]). Hand the multi-leak picture to the shell, which composes
the roadmap and quotes the modules (and any bundle) with savings framing.
```

**Three or more confirmed lever leaks**:
```
[N] of your levers are below benchmark, worth $[X] combined. That many
leaks is a multi-module build. Hand the picture to the shell, which
composes the roadmap in dependency order and quotes the modules or the
matching bundle with honest savings framing. With this many levers
leaking, a bundle is usually the cheaper move.
```

Never quote module or bundle prices here; the shell is the single price source and quotes them at runtime. For a single-lever read, omit this section entirely.

---

## Section 5: The Roadmap (close)

Tell them exactly what to do first. Three parts, advisor voice:

```
WHAT TO PULL FIRST
The winning lever is [name] at +$[X]/day (~$[X]/year). You're at
[ratio] of benchmark on it, that's where the real room is. The module
that builds the fix is [Artemis module] (modules/[slug]/).
Recovering even a tenth of the leak pays it back many times over in
year one; the shell places it on your roadmap and quotes the price.

WHAT I'D WATCH
- [Winning input]: recheck at the quarterly re-run; target [benchmark].
- [Second input]: [threshold].
Baseline saved to velocity-ledger.md. Re-run this diagnostic in 90 days
(velocity-rerun routine) to confirm the lever moved and catch the next
constraint as the others improve.

WHAT'S ALREADY WORKING
[The lever(s) at or above benchmark, name them as strengths to protect.]
```

---

## Fill rules

- Never show a velocity without the one-line math and the leads→opps conversion shown.
- Never name a leaking lever without a $ figure (both $/day and annualized).
- Never recommend an agent without the matching winning lever named and below benchmark.
- Never recommend a partner without the per-lever reason AND the disclosure.
- PLG reports frame velocity off trial-to-paid signups; SLG/Hybrid off opportunities. No invented metrics.
- Round dollars to two significant figures. No false precision.
- Every number traces to a buyer input or a named benchmark substitution.
- If at/above benchmark: no leak, no recommendation, name the strongest lever to protect and close.
