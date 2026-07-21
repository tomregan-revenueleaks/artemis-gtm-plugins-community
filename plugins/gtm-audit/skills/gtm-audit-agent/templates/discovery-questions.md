# Discovery Question Script (GTM Audit: Artemis)

The ordered question script for the audit. Ask **one question at a time**, wait for the answer, branch on it. This mirrors the free flash-audit wizard (Company Profile → Revenue Metrics → Team & Growth → Signal-stack), but conversational and deeper. Save answers to `~/.claude/artemis/gtm-audit/discovery.json` (in Claude Chat, a named Project file) so Diagnose (Step 5) can read them.

Two rules that run through the whole script:
- **Offer the benchmark toggle on every number.** If the buyer doesn't know a figure, say: "No problem; I'll use the industry benchmark for [your industry × motion] for that one. We'll flag it as an estimate so the leak math stays honest." A field filled from the benchmark is AT benchmark by definition and never becomes a leak.
- **Adapt by motion.** Block 3 changes entirely based on the Block 1 motion answer. Never ask PLG companies about SDR quotas; never ask pure-SLG companies about trial-to-paid.
- **Show a one-line position marker at each block transition.** Tell the buyer where they are: "Step 1 of 7 (Company Profile)", "Step 2 of 7 (Revenue Metrics)", and so on. It's a single sentence, not a progress bar, but the buyer always knows how much is left before we diagnose.

---

## Block 1: Company Profile

**Q1. What's the company name and website?**
(Use the domain to ground the audit. If you can infer industry/motion from the site, confirm it rather than asking cold.)

**Q2. What industry are you in?**
Options: SaaS · FinTech · HR Tech · MarTech · Healthcare · EdTech · E-commerce · DevTools · Cybersecurity · AI/ML · Other.
(This picks the benchmark column. If "Other," ask which of the above is closest, the benchmark needs a row.)

**Q3. How big is the team?**
Options: 1–10 · 11–50 · 51–200 · 201–500 · 500+.
(Sizes the leaks and the realistic fix timeline.)

**Q4. What's your primary growth motion?**
Options: PLG (Product-Led) · SLG (Sales-Led) · Hybrid.
(THIS IS THE BRANCH. It decides which benchmark tables apply and which questions Block 3 asks. If they say "both" or hesitate, ask which one drives most of new revenue today, that's the primary motion for benchmarking.)
Name the gotcha before they answer: "The motion answer is the one that trips people up. If you guess Hybrid when you're really SLG, the audit will hunt for PLG leaks that don't exist. When unsure, tell me where most NEW revenue closes."

→ Save Block 1. Confirm back: "Got it: [Company], [industry], [size], [motion]. I'll benchmark you against [industry × motion] peers. Step 2 of 7 (Revenue Metrics), about 6 questions, then the team and growth block."

---

## Block 2: Revenue Metrics

Ask these in order. Offer the benchmark toggle on each.

**Q5. What's your average deal size or ARR per customer?**
(Anchors every dollar figure in the leak report.)

**Q6. How long is your sales cycle, roughly?**
Bands: <30 days · 30–90 · 90–180 · 180+. (Or an exact day count if they have it.)

**Q7. How many new leads do you get per month?**
(Inbound + outbound combined, or split if they track it. For PLG, this is signups/trials, ask for monthly signups instead.)

**Q8. What's your win rate? Of qualified opportunities, what % close?**
Heads up before they answer: "Most people give me win rate of ALL leads here, but I need win rate of QUALIFIED opps. They're very different numbers, and mixing them is the #1 way this report comes out wrong."

**Q9. What's your pipeline coverage? Pipeline value vs quota/target?**
(e.g., "3x" means $3 of pipeline per $1 of target. Skip for pure PLG.)

**Q10. What's your lead-to-opportunity rate? Of leads, what % become real opps?**

→ Save Block 2. "That's the funnel shape. Step 3 of 7 (Team & Growth), a few questions that change based on your motion."

---

## Block 3: Team & Growth (ADAPTIVE, branch on Q4 motion)

### If SLG (Sales-Led):
**Q11. How many SDRs/BDRs do you have?**
**Q12. How many AEs?**
**Q13. What's quota attainment across the team? What % of reps hit quota?**
**Q14. Meetings booked per SDR per month?** (optional, sharpens the outbound leak)
**Q15. Demo-to-proposal rate?** (optional)

### If PLG (Product-Led):
**Q11. What's your trial-to-paid conversion rate?**
**Q12. What's your product activation rate? What % of signups hit the activation milestone?**
**Q13. What's your expansion/net-revenue-retention rate?**
(Do NOT ask about SDRs, quotas, or demos. PLG companies don't run those motions. If they volunteer a small sales team for enterprise, note it but stay on PLG metrics for the benchmark.)

### If Hybrid:
**Q11. What's your trial-to-paid conversion rate?**
**Q12. What's your PQL-to-SQL rate? Of product-qualified leads, what % become sales-qualified?**
**Q13. What % of revenue is sales-assisted vs pure self-serve?**
**Q14. How many SDRs/AEs on the sales-assist side?** (only if they run one)

→ Save Block 3. "Step 4 of 7 (Signal-Stack), the last input block. This tells me whether you can even see your buyers. After this we diagnose."

---

## Block 4: Signal-Stack Check

**Q16. What tools do you run to identify buyers and capture buying signals?**
Read back the tiers so they can place themselves:
- **Tier 1 (Gold):** Koala, Clay, Warmly, Amplemarket
- **Tier 2 (Silver):** Apollo, Demandbase, ZoomInfo
- **Tier 3 (Bronze):** HubSpot Breeze Intelligence (formerly Clearbit), 6sense, Bombora
- **None of these** → No-Tools

(Grade = the highest tier any tool falls into. Note the friction: a Tier-2 stack has data but no real-time activation; No-Tools means anonymous traffic walks out unseen.)

**Q17. Anything else in the stack worth knowing, like CRM, sequencer, conversation intelligence?**
(Catches tech-stack-sprawl leaks and tells you what to adapt the recommendations to. If they already run a partner tool and it works, you'll keep it.)

→ Save Block 4. Then run the tool-connections probe (`tool-connections.md`): offer once to pull the numbers directly from a connected CRM/engagement tool, paste-back otherwise, and record the outcome in `discovery.json`. Discovery is done.

---

## Closing discovery

Confirm the full picture before diagnosing:

> "Okay, here's what I've got: [Company], [industry], [motion], [size]. Funnel: [deal size], [cycle], [leads/mo], [win rate], [lead-to-opp]. [Motion-specific metrics]. Signal tier: [Gold/Silver/Bronze/No-Tools]. [N] of your numbers came from the benchmark; I've flagged those as estimates. Give me a second to benchmark every metric against your industry-and-motion peers and find where you're leaking."

Then run Diagnose (playbook Step 5): hand the metrics to the benchmark-analyst sub-agent and the below-benchmark flags to the leak-quantifier sub-agent (read each `agents/<name>.md` file and spawn a subagent with its contents as the prompt), and assemble the leak-report-template.

---

## Anti-hallucination

Only diagnose against metrics the buyer actually gave (or named benchmark substitutions). A blank field is a blank, not a zero, not a leak. Never ask a PLG company a sales-quota question or an SLG-only company a trial-to-paid question; asking the wrong motion's questions is how invented leaks get in. Track every benchmark-toggle field so the final report can label those figures as estimates.
