# GTM Audit: Artemis GTM Agent Package

This is Artemis GTM's **free** guided diagnostic: it finds exactly where your go-to-market is leaking revenue, quantifies each leak in real dollars per year, and tells you which Artemis agent fixes each one. It's the deep version of the free flash audit, same framework, ten times the depth, run as a real advisor engagement. This is the audit that tells you which of the other agents you actually need before you spend a dollar building anything.

## Install

Your download is `gtm-audit-skill-v1.zip`, a ready-to-upload Claude Skill (SKILL.md sits at the zip root, with its playbook, templates, and resources beside it).

**Claude Chat (claude.ai), no terminal**
1. Keep the `.zip` as-is (don't unzip it).
2. Go to claude.ai/customize/skills and click **Upload skill**, then drop in the `.zip`.
3. Start a new chat and say: **"Start my GTM Audit engagement."**
4. Confirm it loaded: you'll know it worked when the advisor introduces itself and asks a discovery question, not when you get a generic Claude reply. If you get generic Claude, the skill didn't attach. Check that Code execution and file creation and Skills are on under Settings → Capabilities (on Team or Enterprise, an admin enables them), confirm the skill shows at claude.ai/customize/skills, then resend the phrase.
 *(Skills are available on any paid claude.ai plan (Pro, Max, Team, or Enterprise), you just need Code Execution enabled under Settings → Capabilities. On Team/Enterprise, an admin enables Skills under Organization settings.)*

**Claude Code (terminal)**
- Fastest, one command installs your Artemis agents into `~/.claude/skills/`:
 `ARTEMIS_LICENSE_KEY=YOUR-LICENSE-KEY npx github:tomregan-revenueleaks/artemis-skills-installer`
 (your key is in your confirmation email and your library)
- By hand: unzip INTO `~/.claude/skills/gtm-audit-agent/` so `SKILL.md` is at that folder's root (don't copy an unzipped folder in).
- Verify, every time: run `/skills` and confirm the agent appears, then say **"Start my GTM Audit engagement"**, the advisor should introduce itself and ask the first discovery question. If it doesn't, check that `SKILL.md` sits directly inside `~/.claude/skills/gtm-audit-agent/`, not one folder deeper.
- When the audit is done, say yes if it offers to schedule the quarterly re-audit routine.

## What you'll have when you finish

- A GTM health score (0-100) and tier, scored against your industry and growth motion (PLG / SLG / Hybrid)
- A signal-stack grade. Gold / Silver / Bronze / No-Tools, with the reasoning
- Every confirmed revenue leak quantified in annual dollars, with the calculation shown so you can sanity-check it
- The leaks ranked by dollar impact, with cost-of-delay broken out (annual / monthly / weekly / daily)
- For each leak: the matching Artemis build agent (with a buyable link) and the recommended partner platform
- A prioritized build roadmap, what to fix first, second, third, by dollar impact
- In Claude Code: the report and closing summary saved to disk (`~/.claude/artemis/gtm-audit/`), plus a health-score ledger baseline so the quarterly re-audit can chart the trend

This is a diagnostic, so the deliverable is the report and the roadmap, not a built system. The report tells you exactly which build agents to buy and in what order.

## What's in this bundle

```
gtm-audit-skill-v1.zip
├── SKILL.md ← At the zip root (claude.ai loads this)
├── advisor-persona.md
├── accountability-engine.md
├── tool-connections.md

├── claude-setup.md
├── playbook.md ← The source-of-truth audit methodology
├── README.md ← You are here
├── LICENSE.txt
├── templates/
│ ├── discovery-questions.md
│ └── leak-report-template.md
├── agents/
│ ├── benchmark-analyst.md
│ └── leak-quantifier.md
└── routines/
 └── quarterly-reaudit.md
```

## Prerequisites

Before starting, make sure you have:

- Your core GTM numbers, or a willingness to use industry benchmarks as placeholders. ARR or average deal size, sales cycle, monthly leads, win rate, pipeline coverage, lead-to-opp rate
- For SLG/Hybrid motions: rough SDR/AE headcount and quota attainment
- For PLG motions: trial-to-paid, activation rate, expansion figures
- A list of the intent/visitor/enrichment tools you currently run (for the signal-stack grade)
- 30-45 minutes of focused time

You don't need admin access to anything for the audit itself, it's a diagnostic, not a build. The more exact your numbers, the sharper the dollar figures. If you're missing a number, the skill will offer the benchmark and flag it as an estimate.

## Support

Stuck? Have feedback? Found a bug in the benchmark tables? Email tom@artemisgtm.ai. We read every message.

## What this skill is NOT

This is not a sales course. This is not a PDF. This is not a Notion doc. This is an interactive Claude diagnostic that asks you questions one at a time, benchmarks your real numbers against your industry and motion, quantifies each leak in dollars, and hands you a ranked build roadmap.

It also is NOT a build. The GTM Audit diagnoses and prioritizes, it doesn't ship the fix. Each leak it finds maps to a separate build module that does the implementation. Think of this as the free consult that tells you which build modules are worth running, so you spend the build budget on the leaks that actually cost you money.

If you just want to read about the framework, the free flash audit is on the Artemis site, same methodology, none of the depth, none of the per-leak dollar quantification or the advisor walking you through it.

We recommend these tools based on validation across a large body of B2B SaaS GTM audits, not because they pay us. The audit matches a tool to a leak only when that leak is real and quantified, if a tool you already have is working, you keep it.

For current partner pricing and feature details, the skill defers to each partner's website. Always verify there before purchase.

## Pricing

The GTM Audit is free. There's nothing to refund and no purchase terms to accept, it's the top-of-funnel diagnostic. The paid build modules it routes to carry their own terms on their own pages; the audit never quotes a module price in prose, it names the module and the buyer sees the current price at the point of purchase.

Help is freely provided, email tom@artemisgtm.ai if you get stuck during the audit.

## License

Single-user license; see LICENSE.txt for terms. The GTM Audit itself is free. The benchmark tables are re-tuned each quarter as the data moves, re-download from your library any time to run against the latest tables.
