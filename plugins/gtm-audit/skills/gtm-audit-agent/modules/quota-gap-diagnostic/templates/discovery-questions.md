# Discovery Question Script (Quota Gap Diagnostic)

The ordered input script for the diagnostic. Ask **one input at a time**, wait for the answer, then move on. This mirrors the free Quota Attainment Gap Analyzer on the website (Targets → Pipeline → the math), but conversational and deeper, it asks the follow-ups the form can't and resolves the ambiguities the form leaves open. Save answers to `~/.claude/artemis/gtm-engineer/diagnostics/quota-gap/discovery.json` (in Claude Chat, a named Project file) so the gap math (Phase 3) can read them.

**Profile mirror (one discovery, forever).** Before asking anything, read the shared profile at `~/.claude/artemis/_profile/profile.json`. If it exists, confirm the durable facts already there (company, domain, industry, size, motion) in one line rather than re-asking them ("I have you as [company], [motion], still right?"), then ask only the leak-specific inputs below. Write any durable fact you capture here back to `~/.claude/artemis/_profile/profile.json` so the full GTM diagnostic and every build module read it and never re-ask; the leak-specific inputs stay in this calculator's `discovery.json`.

Two rules that run through the whole script:
- **Offer the benchmark toggle on every number.** If the buyer doesn't know a figure, say: "No problem; I'll use the benchmark for that one and flag it as an estimate so the gap math stays honest." A field filled from the benchmark is at benchmark by definition and never becomes the thing that's leaking. Win rate and deal size are special: required pipeline chains off them, so if either is benchmark-filled, the gap is directional and you say so.
- **Confirm the load-bearing ambiguities once.** Two of the website form's quirks get resolved here: whether the pipeline figure is raw or weighted, and what the real lead-to-opp rate is (the form hardcodes 25%).

---

## Block 1: Targets & Team

**Q1. What's the annual revenue target your team carries?**
(Or quarterly, if that's how you think about it. I'll convert. Quarterly = annual ÷ 4, monthly = annual ÷ 12. This anchors every downstream number.)

**Q2. How many quota-carrying AEs do you have?**
(Drives per-AE quota and the capacity check. Closers carrying a number, not SDRs.)

**Q3. What's your average deal size, average contract value (ACV)?**
(Anchors the opps-needed math and the dollar gap. Required pipeline chains off this, so if you're not sure, I'll benchmark it and flag the gap as directional.)

**Q4. What's your win rate, of qualified opportunities, what % close?**
(This is the single most important input. Required pipeline = target ÷ win rate, and required coverage = 1 ÷ win rate. The live tool accepts 5%-60%; anything outside that is almost certainly a units error, if you tell me 70% or 3%, I'll ask whether you're counting opp-to-close or something else before it drives the math.)

**Q5. What's your average sales cycle, in days?**
(Days from opportunity creation to close. Drives the AE capacity check and the time-to-close cutoff. If you only know a range: under 30 → call it 21, 30-90 → 45-60, 90-180 → 135, 180+ → 210.)

→ Save Block 1. Confirm back: "Got it: [$target] across [N] AEs, [win rate]% win rate on [$deal] deals, [cycle]-day cycle. That win rate sets your coverage requirement at [1 ÷ win rate]x, hold that, it's the whole game. Now your current pipeline."

---

## Block 2: Current Pipeline

**Q6. What's the total value of open opportunities in your pipeline right now?**
(This is the number I subtract from required pipeline to get your TRUE gap. The website form never credits this, it sizes activity off the gross target, so this is where the agent beats the form.)

**Q7. Is that number raw open value, or is it win-rate-weighted?**
(I'll use it consistently either way. If it's weighted, your coverage grade reads a touch conservative versus a raw figure, that's fine, I just need to know which so I'm honest about it.)

**Q8. How many days are left in your quarter?**
(I default to today's calendar, [computed days] from the date. Override it if you're planning a future quarter or a custom window. This drives the time-to-close cutoff: the last day a new deal can be created and still close at your cycle length.)

**Q9. What's your lead-to-opportunity rate, of leads, what % become real opps?**
(The website tool hardcodes 25% here. If you track your real rate, give it to me, it sharpens the leads-needed number a lot. If you don't, I'll use 25% and flag it as a benchmark. A real rate well under 25% is itself worth noting; it hints the problem might be upstream of raw volume.)

→ Save Block 2. "That's everything I need. Give me a second to work backward from your number, required pipeline, your real coverage versus what your win rate demands, and the true gap after crediting the pipeline you already have."

---

## Closing discovery

Confirm the full picture before computing:

> "Okay, here's what I've got: [$target] target, [N] AEs, [win rate]% win rate, [$deal] deals, [cycle]-day cycle. Current pipeline [$pipeline] ([raw/weighted]). [Days] days left in the quarter, [lead-to-opp]% lead-to-opp. [N of your numbers came from the benchmark; I've flagged those as estimates.] Computing your coverage and gap now."

Then run the gap math (playbook Phase 3 onward): hand the inputs to the coverage-benchmark-analyst sub-agent and the gap arithmetic to the gap-quantifier sub-agent (read each `agents/<name>.md` file and spawn a subagent with its contents as the prompt), then assemble the gap-report-template.

---

## Anti-hallucination

Only compute against numbers the buyer actually gave (or named benchmark substitutions). A blank field is a blank, not a zero, not a gap. Required pipeline chains off win rate and deal size; if either was benchmark-filled, label the whole gap directional. Never present a benchmark assumption as the buyer's real data. Track every benchmark-toggle field so the final report can label those figures as estimates.
