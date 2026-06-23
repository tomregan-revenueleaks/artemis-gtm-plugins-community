# Discovery Question Script (Stack Grader — Artemis Inventory)

The ordered question script for the grade. Ask **one question at a time**, wait for the answer, branch on it. This mirrors the free Stack Grader tool (Company → Tools by category), but conversational and deeper: it counts unlisted tools and probes utilization, which the one-click tool can't. Save answers to `~/.claude/artemis/stack-grader/discovery.json` (in Claude Chat, a named Project file) so Grade (Step 4) can read them.

Two rules that run through the whole script:
- **Count unlisted tools.** The website pick-list is not the limit. If the buyer names a tool that isn't on the canonical list for a category, it still counts as covering that category. Always ask "anything else in that category?" before moving on, so you don't invent a phantom gap.
- **Adapt by motion and stage.** The two Block 1 answers drive everything: motion sets the category weights, stage sets the must/should/nice map and the stage ARR the dollars multiply against.

---

## Block 1 — Company Profile

**Q1. What's your primary growth motion?**
Options: PLG (Product-Led) · SLG (Sales-Led) · Hybrid.
(THIS RE-WEIGHTS THE EIGHT CATEGORIES. A missing Visitor ID costs a PLG company 22 points and an SLG company only 10. If they say "both" or hesitate, ask which one drives most of new revenue today — that's the primary motion for the weights.)

**Q2. What stage are you, by ARR?**
Options: early ($1-5M) · growth ($5-15M) · scale ($15-50M).
(THIS SETS TWO THINGS: which categories are must-have vs should-have vs nice-to-have at your size, and the stage ARR mid-point the gap dollars multiply against — $3M early, $10M growth, $32.5M scale. If they know their exact ARR, take it; it sharpens every dollar figure. If they only know the band, use the mid-point and flag it.)

→ Save Block 1. Confirm back: "Got it: a [stage] company running a [motion] motion. I'll weight Visitor ID at [weight] and Sales Engagement at [weight], grade your coverage against what a [stage] team is expected to run, and price gaps against [$ stage mid-point or your exact ARR]. Now let's inventory the stack, category by category."

---

## Block 2 — Stack Inventory (walk all 8 categories, one at a time)

Ask these in order. For each, read back a few common tools so the buyer can place themselves, then ALWAYS ask "anything else in that category, even if it's not on that list?"

**Q3. CRM** — customer relationship management.
Common: HubSpot, Salesforce, Attio, Pipedrive, Close, Freshsales, Zoho CRM. (Foundational; almost every team should have one.)

**Q4. Sales Engagement** — outbound sequencing & prospecting.
Common: Amplemarket, Outreach, Salesloft, Apollo, Lemlist, Instantly, Smartlead, Mixmax.

**Q5. Data Enrichment** — contact & company data quality.
Common: Amplemarket, Apollo, ZoomInfo, Cognism, Clearbit, Lusha, LeadIQ, Seamless.AI.

**Q6. Conversation Intelligence** — call recording & sales coaching.
Common: Attention, Gong, Chorus.ai, Sybill, Fireflies.ai, Avoma, Jiminny.

**Q7. Visitor Identification** — website visitor ID & intent signals.
Common: Warmly, RB2B, Clearbit Reveal, Leadfeeder, Demandbase, 6sense, Albacross.

**Q8. Revenue Intelligence** — pipeline analytics & forecasting.
Common: Clari, Aviso, BoostUp, People.ai, Ebsta, InsightSquared.

**Q9. Pipeline Management** — deal tracking & workflow automation.
Common: Rattle, Scratchpad, Dooly, DealHub, Troops, or native CRM tools.

**Q10. Sales Enablement** — content management & training.
Common: Highspot, Seismic, Showpad, Guru, Mindtickle, Spekit.

For each category, record: covered (1+ tools) or not, and the exact tool list. Note any category with 3+ tools — that's a redundancy candidate.

→ Save Block 2. "That's the inventory. Two of these categories have three or more tools — I'll flag those as possible sprawl. Last thing before I grade: a quick read on whether these tools are actually used."

---

## Block 3 — Utilization + Integration Probe

This is the depth the one-click tool can't reach. For each COVERED category (don't ask about gaps), one quick read:

**Q11. For each covered category: is the tool actually used (logged into weekly, in the workflow) or is it shelfware?**
(A covered category full of shelfware still scores as covered — coverage is binary — but you flag it in the narrative. "You have a tool here, but if nobody uses it, the coverage is on paper only.")

**Q12. Is each tool wired to your CRM, or standalone?**
(Standalone tools fragment the data. This sharpens the consolidation narrative and the redundancy call — two tools doing the same job where one is shelfware is the clearest cut.)

→ Save Block 3. Then run the tool-connections probe (`tool-connections.md`): offer once to pull the real tool ledger directly from a connected CRM/billing tool, paste-back otherwise, and record the outcome in `discovery.json`. Discovery is done.

---

## Closing discovery

Confirm the full picture before grading:

> "Okay, here's what I've got: a [stage] [motion] company. Your stack: [list covered categories with their tools]. Gaps I can already see: [list uncovered categories]. Sprawl I'll check: [any 3+-tool categories]. I'm pricing against [$ stage mid-point or exact ARR]. Give me a second to weight every category by your motion, score the coverage, charge the redundancy drag, and price each gap and each redundancy in dollars."

Then run Grade (playbook Step 4): hand the motion, stage, and tool list to the benchmark-analyst sub-agent and the confirmed gaps and redundancies to the gap-quantifier sub-agent (read each `agents/<name>.md` file and spawn a subagent with its contents as the prompt), and assemble the stack-report-template.

---

## Anti-hallucination

Only grade against the tools the buyer actually named (count unlisted ones). A category they didn't mention is a gap candidate — but only flag it as a real gap if it's in the stage's must/should/nice map. Never invent a tool to fill a category, and never call two complementary tools in different categories a redundancy. Track whether you used the stage mid-point or an exact ARR so the final report can label the dollar figures correctly.
