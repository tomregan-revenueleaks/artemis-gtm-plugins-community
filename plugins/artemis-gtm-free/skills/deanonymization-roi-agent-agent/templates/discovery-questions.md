# Discovery Question Script (Website Visitor ROI Calculator — Artemis Mirage)

The ordered question script for the calculator. Ask **one question at a time**, wait for the answer, branch on it. This mirrors the free Website Visitor ROI Calculator on the site (monthly visitors + deal size), but conversational and deeper — it adds the traffic-mix and ICP-fit questions the two-field form can't ask, because those are what make the figure honest. Save answers to `~/.claude/artemis/deanonymization-roi-agent/discovery.json` (in Claude Chat, a named Project file) so Phase 3 (Quantify) can read them.

Two rules that run through the whole script:
- **Offer the benchmark on any number the buyer doesn't have.** If they don't know a figure, say: "No problem; I'll use the Artemis benchmark for that one. We'll flag it as an estimate so the figure stays honest." The two anchors — visitors and deal size — matter most; everything else is a funnel rate with a conservative default.
- **Stay in scope.** This calculator measures anonymous traffic only. If a different leak surfaces (slow response, no scoring, weak pages), log it as a side observation and point at the free GTM Audit — don't chase it here.

---

## Block 1 — Traffic Profile

**Q1. How many visitors does your website get per month?**
(From Google Analytics or your analytics tool — total monthly sessions, last 30 days. This is the top of the funnel; every account downstream scales off it. If they don't know, offer the analytics number, or benchmark it from their industry and flag it.)

**Q2. What's the traffic mix — US vs international, paid vs organic, B2B vs consumer?**
(This is the tool-selection driver. Listen for:
- Mostly US B2B, lower volume → RB2B territory
- Enterprise/international mix, US/EU coverage → Warmly territory
- Consumer/B2C heavy → flag it; identification rates don't hold on consumer traffic, don't run a B2B funnel on B2C numbers.)

**Q3. How much of that traffic is plausibly your ICP — the right company size, industry, geography?**
(The share that looks like a real buyer. If lots of it is job-seekers, students, or competitors, the recoverable pipeline is much smaller than the raw count. This is one of the two numbers — with deal size — that moves the figure most. If they don't know, use a conservative ICP-fit share and flag it.)

→ Save Block 1. Confirm back: "Got it: [X] monthly visitors, mostly [mix], roughly [Y]% ICP-fit. That points the identification benchmark at [rate] and the tool at [Warmly / RB2B / hybrid]. Now the deal size — it anchors every dollar figure."

---

## Block 2 — Deal & Funnel Inputs

Ask these in order.

**Q4. What's your average deal size (ACV)?**
(Average closed-won deal value from the CRM. This is the anchor — pipeline = opportunities × ACV, so a wrong ACV moves the whole figure. Push gently for a real number. If they truly don't have it, use the industry benchmark and flag loudly that the whole figure is an estimate until they confirm ACV.)

**Q5 (optional). What's your current form-fill rate — the share of visitors who fill a form today?**
(The 127-audit median is 1.8%. Used in the reality check, not the funnel: far below 1.8% means the anonymous leak is even larger; well above 1.8% means more demand is already self-identifying, so trim the recoverable slice. If they don't track it, skip — it's a sharpener, not a requirement.)

**Q6 (optional). Do you know your real meeting-to-opportunity rate — what share of booked meetings become qualified opps?**
(If they know it, use it in place of the 50% default. If not, use 50% and label it.)

→ Save Block 2. "That's everything I need. The two numbers I'll lean on hardest are your deal size and your ICP-fit traffic share — those move the figure most. Give me a second to run the de-anon funnel and show you what your anonymous traffic is worth."

---

## Closing discovery

Confirm the full picture before quantifying:

> "Okay, here's what I've got: [X] monthly visitors, [mix], ~[Y]% ICP-fit, [$Z] ACV. [N] of these came from a benchmark, and I've flagged those as estimates. I'll run the funnel at a conservative [identification rate] for your traffic mix, count meetings at the account level, and show you both an expected figure and an upper bound so you've got a real range. One moment."

Then run Quantify (playbook Phase 3): hand the traffic profile to the benchmark-analyst sub-agent to set the identification rate and ICP-fit-adjusted visitor count, and the result to the pipeline-quantifier sub-agent for the dollar math (read each `agents/<name>.md` file and spawn a subagent with its contents as the prompt), then assemble the leak-report-template.

---

## Anti-hallucination

Only quantify off the visitors and deal size the buyer actually gave (or named benchmark substitutions). A blank field is a blank — not a zero. Never run the funnel on raw traffic when a big share is non-ICP; discount to the ICP-fit slice first. Never count meetings off contacts — meetings book at the account level. Never present the output as closed revenue — it's pipeline created. Track every benchmark-filled field so the final report can label those figures as estimates. Stay in scope: anonymous traffic only; point other leaks at the free GTM Audit.
