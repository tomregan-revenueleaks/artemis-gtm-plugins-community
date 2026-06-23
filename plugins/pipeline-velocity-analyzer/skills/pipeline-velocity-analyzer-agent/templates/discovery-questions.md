# Discovery Question Script (Pipeline Velocity — Artemis Throughput)

The ordered question script for the diagnostic. Ask **one input at a time**, wait for the answer, branch on it. This mirrors the free Pipeline Velocity Calculator wizard (Pipeline Profile → The Four Inputs), but conversational and deeper — and it adds the one input the self-serve tool skips: the lead-to-opportunity rate. Save answers to `~/.claude/artemis/pipeline-velocity/discovery.json` (in Claude Chat, a named Project file) so Compute (Phase 3) can read them.

Two rules that run through the whole script:
- **Offer the benchmark toggle on every number.** If the buyer doesn't know a figure, say: "No problem; I'll use the [industry] × [motion] benchmark for that one. We'll flag it as an estimate so the velocity math stays honest." A field filled from the benchmark is AT benchmark by definition and never becomes a leaking lever.
- **Leads are not opportunities.** Always collect the lead-to-opp rate and convert (opps = leads × lead-to-opp) before running the velocity formula. This is the single thing the agent does that the website calculator doesn't.

---

## Block 1 — Pipeline Profile

**Q1. What industry are you in?**
Options: SaaS · FinTech · HR Tech · MarTech · Healthcare · EdTech · E-commerce · DevTools · Cybersecurity · AI/ML · Other.
(This picks the benchmark row. If "Other," ask which of the above is closest — the benchmark needs a row.)

**Q2. What's your primary growth motion?**
Options: SLG (Sales-Led) · PLG (Product-Led) · Hybrid.
(THIS IS THE BRANCH. It sets the benchmark row AND how velocity is framed:
- **SLG** — velocity runs on opportunities; a sales team closes them. The default path.
- **Hybrid** — velocity runs on the sales-assisted opportunities; Hybrid benchmark row.
- **PLG** — "leads" means signups/trials, "win rate" means trial-to-paid, "deal size" means ARR per paid account, "cycle" means time-to-convert. PLG benchmark row.
If they say "both" or hesitate, ask which drives most of new revenue today — that's the primary motion for benchmarking.)

→ Save Block 1. Confirm back: "Got it: [industry], [motion]. I'll benchmark you against the [industry] × [motion] row and frame velocity off [opportunities / trial-to-paid signups]. Now your four numbers."

---

## Block 2 — The Four Inputs

Ask these in order, one at a time. Offer the benchmark toggle on each.

**Q3. How many leads do you get per month?**
(Inbound + outbound combined. For PLG, this is monthly signups/trials. Rough is fine.)

**Q4. What's your lead-to-opportunity rate? Of those leads, what % become real opportunities?**
(THE INPUT THE WEBSITE CALCULATOR IGNORES. Opportunities = monthly leads × this %. If they don't know it, benchmark it for their industry and say so — never assume 100% and never run raw leads as opps. For PLG, this maps to activation-to-tracked-unit if relevant; if every signup is already the unit they convert, it can be 100%, but ask, don't assume.)

**Q5. What's your average deal size (ACV)?**
(Total revenue from closed deals ÷ number of closed deals. For PLG, the ARR per paid account. Anchors every dollar figure in the velocity.)

**Q6. What's your win rate? Of opportunities, what % close?**
(Closed-won ÷ opportunities created. For PLG, trial-to-paid %.)

**Q7. What's your average sales cycle, in days?**
(Opportunity create to close. Bands if they only know a range: <30 (use 21) · 30-90 (use 60) · 90-180 (use 135) · 180+ (use 210). For PLG, time-to-convert. This is the velocity denominator — shorter is faster.)

→ Save Block 2. Then convert and confirm: "So your opportunities are [leads] × [lead-to-opp]% = [opps]/month. With a [deal size] ACV, a [win rate]% win rate, and a [cycle]-day cycle, give me a second to compute your velocity and benchmark it."

---

## Closing discovery

Confirm the full picture before computing:

> "Okay, here's what I've got: [industry], [motion]. [leads] leads/mo × [lead-to-opp]% = [opps] opportunities/mo, [deal size] ACV, [win rate]% win rate, [cycle]-day cycle. [N] of these came from the benchmark; I've flagged those as estimates and they won't become a leaking lever. Computing your velocity now."

Then run Compute (playbook Phase 3): convert leads to opps, compute velocity in $/day, annualize it. Hand the velocity and inputs to the benchmark-analyst sub-agent (Phase 4) and the inputs to the lever-quantifier sub-agent (Phase 5) — read each `agents/<name>.md` file and spawn a subagent with its contents as the prompt — then assemble the velocity-report-template.

---

## Anti-hallucination

Only compute against inputs the buyer actually gave (or named benchmark substitutions). A blank field is a blank — not a zero. Never run raw leads through the velocity formula as opportunities; always convert with the lead-to-opp rate first. Never ask a PLG buyer for an SLG-only number framed the SLG way; reframe leads/win/deal/cycle into PLG terms. Track every benchmark-toggle field so the final report can label those figures as estimates.
