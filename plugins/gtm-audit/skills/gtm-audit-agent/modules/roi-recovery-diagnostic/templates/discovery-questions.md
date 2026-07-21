# Funnel Intake Script (Recoverable Revenue ROI)

The ordered question script for the diagnostic. Ask **one question at a time**, wait for the answer, save it. This mirrors the seven fields of the free Sales ROI Calculator (ARR → deal size → leads → lead-to-opp → win rate → cycle → AEs), but conversational and deeper. Save answers to `~/.claude/artemis/gtm-engineer/diagnostics/roi-recovery/discovery.json` (in Claude Chat, a named Project file) so the math (Steps 3-6) can read them.

**Profile mirror (one discovery, forever).** Before asking anything, read the shared profile at `~/.claude/artemis/_profile/profile.json`. If it exists, confirm the durable facts already there (company, domain, industry, size, motion) in one line rather than re-asking them ("I have you as [company], [motion], still right?"), then ask only the leak-specific inputs below. Write any durable fact you capture here back to `~/.claude/artemis/_profile/profile.json` so the full GTM diagnostic and every build module read it and never re-ask; the leak-specific inputs stay in this calculator's `discovery.json`.

Two rules that run through the whole script:
- **Offer the benchmark toggle on every number.** If the buyer doesn't know a figure, say: "No problem; I'll use the benchmark for that one. We'll flag it as an estimate so the recoverable math stays honest." A field filled from the benchmark is AT benchmark by definition and never becomes a gap.
- **Track real vs benchmark-filled.** Mark each saved field so Step 3 only ever quantifies gaps on REAL inputs.

These are the exact seven inputs the website calculator takes. Don't add fields it doesn't have; don't skip ones it does.

---

## Q1. What's your current ARR?

Total annual recurring revenue. (The form default is $3M; presets run $500K / $1M / $3M / $5M / $10M / $25M.)

This one is context, not a lever. It sizes the opportunity and sanity-checks the result, the recoverable figure can never exceed plausible total revenue, so ARR is the ceiling we check the answer against. If they don't want to share an exact number, a band is fine.

→ Save. Confirm: "Got it. That's the context number; now the funnel itself."

---

## Q2. What's your average deal size?

CRM average won deal value (ACV). (Form default $25K; presets $5K / $10K / $25K / $50K / $100K / $250K.)

This is the multiplier under every dollar figure in the diagnostic, so it's worth getting right. Formula if they need it: total revenue from closed deals ÷ number of closed deals. Watch for a units slip, deal size entered in thousands ($25 instead of $25,000) throws the whole figure off by 1000x.

→ Save.

---

## Q3. How many new leads do you get per month?

Total inbound leads per month, any source. (Form default 200; presets 50 / 100 / 250 / 500 / 1K / 5K.) Rough is fine; last 30 days.

The model annualizes this (× 12), so a monthly number is what we want. The classic units error is pasting an ANNUAL lead count into the monthly field, if the number looks 10-12x high, re-confirm it's monthly before it touches the math.

→ Save.

---

## Q4. What's your lead-to-opportunity rate?

Of your leads, what % become real opportunities? (Form default 15%; slider 1-50%; presets 5% / 10% / 15% / 25% / 35%.) Formula: opportunities ÷ total leads × 100.

This is the first conversion lever, and for most companies it's the one carrying the biggest recoverable figure. The top-quartile benchmark is 25%; the realistic median is ~18%. If they don't track it, offer the benchmark and flag it, but note that a benchmark-filled lead-to-opp can't later become a gap.

→ Save. (Mark real or benchmark-filled.)

---

## Q5. What's your win rate?

Of qualified opportunities, what % close? (Form default 35%; slider 1-80%; presets 15% / 25% / 35% / 45% / 55%.) Formula: closed-won ÷ opportunities × 100.

The second conversion lever. Top-quartile benchmark 50%; realistic median ~28%. Be honest with the buyer that 50% is top-quartile and rare, most companies sit well below it, which is exactly why the recoverable figure against that bar is a stretch target, not a promise.

→ Save. (Mark real or benchmark-filled.)

---

## Q6. How long is your sales cycle, in days?

Average days from opportunity created to closed. (Form default 90; slider 15-365; presets 30 / 60 / 90 / 120 / 180 / 365.)

This is the velocity lever, and it's handled DIFFERENTLY from the two above: a faster cycle realizes the same deals sooner, it doesn't create new ones, so it is reported as days-faster plus cash pulled forward, NEVER added to the recoverable total. Tell the buyer that up front so the separate treatment doesn't surprise them at the report. Benchmark is 60 days for mid-market.

→ Save.

---

## Q7. How many AEs do you have?

Current AE headcount. (Form default 4; slider 1-50; presets 1 / 2 / 4 / 8 / 15 / 25.)

Context for the cycle-velocity read, faster cycles let each AE run more deals per year, but that capacity only becomes revenue if there's lead supply to fill it (which is why cycle is velocity, not recoverable ARR). Not a benchmarked lever; just capture it.

→ Save. The seven calculator inputs are done.

---

## Q8 (context, not a calc input). What CRM and prospecting/enrichment tools are you running today?

This one does NOT change the number; it changes which fix I recommend and whether you adapt what you have or add a tool. A quick read of your stack (the CRM, plus anything you use for enrichment, signals, or sequencing) is enough.

→ Save to `discovery.json` under a `connections` key (e.g. `{ "crm": "...", "enrichment": "...", "notes": "..." }`). Step 6 / playbook 7.3 reads this to pick the right partner for THIS buyer: a lead-to-opp gap on a CRM they like leads with Amplemarket for the enrichment/signal layer (Attio only as the migration option if the CRM can't hold a lead score); a thin or no CRM leads with Attio as the system of record first, because a lead score with nowhere to live is the more common failure. Name both, then pick the one their stack needs and say why. Leave it blank and the fix-map still works, it just stays generic.

---

## Closing intake

Confirm the full funnel before computing:

> "Okay, here's your funnel: [ARR] ARR, [deal size] average deal, [leads]/month, converting [lead-to-opp]% to opps, winning [win rate]% of those, on a [cycle]-day cycle, with [AEs] AEs. [N] of your numbers came from the benchmark; I've flagged those as estimates and won't turn them into gaps. Give me a second to run the recoverable-revenue math: one optimized funnel minus your current funnel, no double-counting, with cycle reported separately as velocity."

Then run the math (playbook Steps 2-6): hand the metrics to the benchmark-analyst sub-agent and the recoverable computation to the roi-quantifier sub-agent (read each `agents/<name>.md` file and spawn a subagent with its contents as the prompt), and assemble the roi-report-template.

---

## Anti-hallucination

Only quantify against metrics the buyer actually gave (or named benchmark substitutions). A blank field is a blank, not a zero, not a gap. Capture exactly the seven calculator inputs for the math, no more, no fewer; Q8 is context for the fix-map only and never enters a dollar figure. Track every benchmark-toggle field so the final report can label those figures as estimates, and so a benchmark-filled lever never becomes a gap graded against itself.
