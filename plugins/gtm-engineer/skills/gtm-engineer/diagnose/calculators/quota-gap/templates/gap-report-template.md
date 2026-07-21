# Gap Report Template (Quota Gap Diagnostic)

This is the deliverable. The whole engagement converges here: a dollar-framed quota gap report the buyer screenshots and forwards to their team. Fill it from the Coverage & Gap, Activity, Diagnose, and Close phases (playbook Phases 3-6). Every number traces to an input the buyer gave or a named benchmark substitution.

Keep the buyer's voice and numbers throughout. Don't pad. A CFO should be able to read this in three minutes and know whether the team makes plan, what the gap costs, and the one thing to fix.

---

## Section 1: Executive Header

```
═══════════════════════════════════════════════════
 QUOTA ATTAINMENT GAP
 [Company / Team] · [Annual Target] · [Growth Motion]
 Analyzed [Date]
═══════════════════════════════════════════════════

PIPELINE COVERAGE: [X.X]x of [Y.Y]x required at a [win rate]% win rate GRADE [A-F]
TRUE QUARTERLY GAP: $[X] (net of $[current] already in pipeline)
COST OF DELAY: $[X]/week every week this stays open
DIAGNOSIS: [VOLUME / CONVERSION / CAPACITY / ON TRACK]
```

**Coverage grade bands** (set from sufficiency = coverageRatio ÷ requiredCoverage, the same canonical bands as the live calculator; never use any other scheme):
- 100%+ of required, **A**. Covers your win rate.
- 75-99%, **B**. Nearly enough coverage.
- 50-74%, **C**. Half the coverage you need.
- 25-49%, **D**. Thin pipeline.
- Below 25%, **F**. Critical gap.

**Coverage line**, one sentence on coverage versus the requirement:
> "1.8x coverage against the 4.5x your 22% win rate requires, that's 40% of what you need, a Grade D. You're structurally short of plan before a single deal slips."

---

## Section 2: The Diagnosis (one paragraph)

Plain-language summary of whether they make plan and why, in the advisor's voice. No jargon. Name the single primary problem and the through-line.

> "You're sourcing leads fine and your AEs have room. The problem is coverage: at a 22% win rate you need 4.5x quarterly pipeline and you're carrying 1.8x, so you're $1.27M of pipeline short this quarter. That's a volume problem, not enough is entering. One fix closes most of it."

---

## Section 3: What We See (the math, laid out)

```
WHAT WE SEE
───────────────────────────────────────────────────
Coverage: [current]x vs [required]x required (Grade [A-F])
 At a [win rate]% win rate, required = 1 ÷ win rate = [Y.Y]x
Pipeline gap: $[gap] this quarter
 [$annualTarget] ÷ 4 ÷ [win rate]% = $[req] required
 − $[current] in pipeline = $[gap]
Activity: [leads]/mo leads · [opps]/mo opps · [deals]/mo deals to close it
 $[gap] ÷ 3 ÷ $[deal] = [opps] opps/mo ÷ [lead-to-opp]% = [leads] leads/mo
AE load: [active opps]/AE active ([Healthy / Near / Over] capacity, ceiling 25)
Time to close: cutoff [date] ([Comfortable runway / Window is tight / New deals won't close])
```

When there's no gap (`pipelineGap ≤ 0`), say so on the activity line: "No new activity required, your pipeline already covers the gap." Don't manufacture work.

### Cost-of-delay math (annualize the quarterly gap)
From the quarterly gap: annual = gap × 4 · monthly = annual ÷ 12 · weekly = annual ÷ 52 · daily = annual ÷ 365. This is a *pipeline* shortfall, not booked-revenue loss, quote closed-revenue-at-risk (× win rate) separately so it's not mistaken for a booking miss. "Every week you wait, that's $[X] of pipeline you stay short."

---

## Section 4: The Diagnosis → The Fix (one problem, one agent)

The diagnosis lands on exactly one primary problem via the precedence (capacity → conversion → volume → on-track). Recommend the ONE matching agent. Never a catalog, never a bundle.

```
🔴 DIAGNOSIS: [VOLUME / CONVERSION / CAPACITY]
───────────────────────────────────────────────────
Why: [one-line evidence, e.g. "1.8x coverage against the 4.5x a 22% win rate needs"]
Costs: $[gap] this quarter · $[weekly]/week in pipeline you stay short

THE FIX
Build: [Artemis module] · modules/[slug]/
 The shell places it on your roadmap and quotes the price.
Partner: [Tool], [why this tool for THIS diagnosis].
 (If you already run [alternative] and it's working, keep it; the
 agent adapts to your stack.)
```

### The canonical diagnosis → module map (as shipped)

| Primary problem | Artemis module | Module path | Partner |
|-----------------|----------------|-------------|---------|
| Volume (under-covered, not enough pipeline) | Outbound System | `modules/outbound/` | Amplemarket + Warmly |
| Conversion (win rate < 20%) | Conversion Content System | `modules/conversion/` | Attention |
| Capacity (AEs > 25 active opps) | Qualification Automation System | `modules/qualification/` | Amplemarket |
| On track (covered + healthy capacity + win rate ≥ 20%) | (optional Lead Scoring to protect pipeline) | `modules/scoring/` | Attention (optional) |

Route every module per the module-gate doctrine: log the gap, the shell places it on the roadmap and quotes the price. Never a prose price, a checkout link, or a codename.

### The partner per diagnosis (with disclosure, every time)

| Diagnosis / need | Partner | go/ slug |
|------------------|---------|----------|
| Volume, add qualified pipeline via sequencing | Amplemarket | `artemisgtm.ai` |
| Volume, capture in-market inbound demand | Warmly | `artemisgtm.ai` |
| Conversion, find why deals stall, coach the win-rate lift | Attention | `artemisgtm.ai` |
| Capacity, automate qualification + follow-up off the AEs' plates | Amplemarket | `artemisgtm.ai` |

---

## Section 5: On Track (use this section instead of Section 4 when the diagnosis is on-track)

When the math says covered gap AND healthy capacity AND win rate ≥ 20%, don't manufacture a problem:

```
+ ON TRACK
───────────────────────────────────────────────────
Your [X.X]x coverage clears the [Y.Y]x your [win rate]% win rate requires,
your AEs are at [N] active opps (under the 25 ceiling), and your win rate is
healthy. You're on track to make plan this quarter. Nothing to buy.

OPTIONAL, protect the pipeline
If you want to keep the edge as you scale, the Lead Scoring module
(modules/scoring/) routes the highest-fit opps first so coverage quality
holds. Optional, not a fix; the shell quotes the price if you pursue it.
```

---

## Section 6: The Roadmap (close)

Tell them exactly what to do. Three parts, advisor voice:

```
WHAT TO FIX FIRST
Your gap is a [volume/conversion/capacity] problem worth $[gap] this quarter.
The module that closes it is [Artemis module] (modules/[slug]/). Start there; the
shell quotes the price and the partner platform under it is [Tool].
(If on track: nothing to fix, here's the optional protect-the-pipeline option.)

WHAT I'D WATCH
- Win rate: if it climbs past [25]%, required coverage drops to [X]x and the gap shrinks.
- AE load: if active opps cross 25, the diagnosis flips to capacity.
- Time-to-close: the cutoff is [date], last day a new deal can close this quarter.

THE TRENDLINE
Baseline saved to coverage-ledger.md. Re-run this diagnostic at the start of
next quarter (quarterly-recheck routine) to watch coverage climb and the gap
close, and to catch the diagnosis flipping as you fix this one.
```

---

## Fill rules

- Never show the gap without a $ figure and a cost-of-delay.
- Never recommend an agent without the matching diagnosis named.
- Never recommend a partner without the per-diagnosis reason AND the disclosure.
- One diagnosis, one agent. Never a bundle off a single quota gap, point whole-funnel buyers to the GTM Audit instead.
- Net out current pipeline before sizing the gap, every time.
- Grade coverage against 1 ÷ win rate, never a static 3-4x band.
- Round dollars to two significant figures. No false precision.
- Every number traces to a buyer input or a named benchmark substitution.
