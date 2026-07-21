# Discovery Question Script (Lead Response ROI)

The ordered question script for the diagnostic. Ask **one question at a time**, wait for the answer, move to the next. This mirrors the free Lead Response ROI Calculator's four inputs (Monthly Leads → Deal Size → Response Time → Conversion Rate), but conversational and with the benchmark toggle on each. Save answers to `~/.claude/artemis/gtm-engineer/diagnostics/lead-response/discovery.json` (in Claude Chat, a named Project file) so Diagnose (Step 4) can read them.

**Profile mirror (one discovery, forever).** Before asking anything, read the shared profile at `~/.claude/artemis/_profile/profile.json`. If it exists, confirm the durable facts already there (company, domain, industry, size, motion) in one line rather than re-asking them ("I have you as [company], [motion], still right?"), then ask only the leak-specific inputs below. Write any durable fact you capture here back to `~/.claude/artemis/_profile/profile.json` so the full GTM diagnostic and every build module read it and never re-ask; the leak-specific inputs stay in this calculator's `discovery.json`.

Two rules that run through the whole script:
- **Offer the benchmark toggle on every number.** If the buyer doesn't know a figure, say: "No problem; I'll use the benchmark for that one and flag it as an estimate so the leak math stays honest." A field filled from the benchmark is labeled directional, not their real data.
- **Get the conversion-rate framing right.** The conversion rate is the buyer's CURRENT rate at their CURRENT response time. It already includes the slow-response penalty. We back-solve the ceiling from it; we never decay it again. The script makes this explicit at Q4.

---

## Q1: Monthly inbound leads

**Q1. How many inbound leads do you get per month?**
Any source, last 30 days, rough is fine. (CRM dashboard or marketing-automation tool, total leads per month.)

*Benchmark toggle:* if they don't know, offer a placeholder and label it. This is the volume the whole leak scales with, so a real number matters.

→ Save Q1. "Got it. Now your deal size, so we can put the leak in dollars."

---

## Q2: Average deal size

**Q2. What's your average deal size?**
Average won deal value or ACV. (From the CRM under "average won deal value.")

*Benchmark toggle:* if unsure, offer a placeholder and label it, but a real deal size is easy to find and anchors every dollar figure, so push gently for the real one.

→ Save Q2. "Now the input that drives everything: how fast you respond."

---

## Q3: Current average response time

**Q3. On average, how long from when a lead submits a form to when a human first contacts them?**
Map their answer to one of six tiers. Read them back so they can place themselves:
- **Under 5 minutes**, the conversion ceiling
- **5-30 minutes**
- **30-60 minutes**
- **1-4 hours**
- **4-24 hours**
- **24+ hours**

*If they don't know it,* estimate from how routing works today and label it an estimate:
- Automated routing + alerts + an SLA → 5-30 min or better
- Reps watching notifications, no formal SLA → 30-60 min or 1-4 hours
- Manual triage, shared inbox, no alerting → 4-24 hours
- "Someone gets to it eventually" → 24+ hours

The Artemis median across a large body of B2B SaaS GTM audits is **42 hours** (the 24+ tier). If they truly can't say, offer the **4-24 hour** tier as a deliberately conservative placeholder (not the 24+ median, so an assumption doesn't inflate the leak) and tell them you're being conservative.

→ Save Q3. Place it against the median out loud: "42 hours is the Artemis median across a large body of B2B SaaS GTM audits; you're at [tier], which is [faster/slower/around] that." Then: "Last input, and it's the one people get wrong, so I'll be precise about it."

---

## Q4: Lead-to-opportunity rate (the trap input)

**Q4. Of your leads, what percent become real opportunities, at your current response time?**

Frame it explicitly so they give you the right number, and so they understand what you're about to do with it:

> "I want the rate you actually get today, at your current response speed, not a target. That number already includes whatever you're losing to slow follow-up. I'm going to work out how much higher it would be if you responded in under five minutes, and the gap between the two is the leak. So I won't penalize your number twice. I back-solve the ceiling from it."

*Benchmark toggle:* if they don't track it, offer the **12% B2B median** and flag that the whole leak pivots on this number, so it's directional until they swap in the real one.

→ Save Q4. Discovery is done.

---

## Closing discovery

Confirm the full picture before diagnosing:

> "Okay, here's what I've got: [N] leads/month, $[deal] average deal, [tier] response time, [X]% lead-to-opp at that speed. [N of your numbers came from the benchmark; I've flagged those as estimates.] Give me a second to back-solve your sub-5-minute ceiling and quantify the gap."

Then run Diagnose (playbook Step 4): map the tier to its multiplier, back-solve the ceiling (`min(1, enteredRate ÷ multiplier)`), compute `revenueLostPerYear = (ceiling − current) × leads × deal × 12`, and assemble the leak-report-template. You can hand the placement to the benchmark-analyst sub-agent and the dollar math to the leak-quantifier sub-agent (read each `agents/<name>.md` file and spawn a subagent with its contents as the prompt).

---

## Anti-hallucination

Only diagnose against numbers the buyer actually gave (or named benchmark substitutions). A blank field gets the benchmark, labeled as an estimate, never a silent zero. Never decay the entered conversion rate twice: it is the CURRENT rate, and the ceiling is back-solved UP from it. Track every benchmark-toggle field so the final report can label those figures as estimates, especially the conversion rate, which the whole leak pivots on.
