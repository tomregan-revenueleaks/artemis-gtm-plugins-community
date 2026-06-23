# Sales Tech Stack Grader — Artemis GTM Agent Package

This is Artemis GTM's **free** guided stack diagnostic: it grades your B2B sales tech stack across the eight GTM categories, finds the coverage gaps and the redundant tools, and prices every one in real dollars per year. It's the deep version of the free Stack Grader tool on the site — same scoring math, ten times the depth, run as a real advisor engagement. This is the grade that tells you exactly what to add first, what to cut first, and what each one is worth before you spend a dollar consolidating anything.

## Install

Your download is `stack-grader-agent-skill-v1.zip` — a ready-to-upload Claude Skill (SKILL.md sits at the zip root, with its playbook, templates, and resources beside it).

**Claude Chat (claude.ai) — no terminal**
1. Keep the `.zip` as-is (don't unzip it).
2. Go to claude.ai/customize/skills and click **Upload skill**, then drop in the `.zip`.
3. Start a new chat and say: **"Start my Sales Tech Stack Grader engagement."**
 *(Skills are available on every claude.ai plan, including Free — you just need Code Execution enabled under Settings → Capabilities. On Team/Enterprise, an admin enables Skills under Organization settings.)*

**Claude Code (terminal)**
- Fastest — one command installs your Artemis agents into `~/.claude/skills/`:
 `npx github:tomregan-revenueleaks/artemis-skills-installer YOUR-LICENSE-KEY`
 (your key is in your confirmation email and your library)
- By hand: unzip INTO `~/.claude/skills/stack-grader-agent/` so `SKILL.md` is at that folder's root (don't copy an unzipped folder in).
- Verify, every time: run `/skills` and confirm the agent appears, then say **"Start my Sales Tech Stack Grader engagement"** — the advisor should introduce itself and ask the first discovery question. If it doesn't, check that `SKILL.md` sits directly inside `~/.claude/skills/stack-grader-agent/`, not one folder deeper.
- When the grade is done, say yes if it offers to schedule the quarterly re-grade routine.

## What you'll have when you finish

- A Coverage Score (0-100), letter grade (A-F), and maturity tier, scored against your growth motion (PLG / SLG / Hybrid) and stage (early / growth / scale)
- A coverage map across all eight categories — covered, gap, or missing — each carrying its motion weight
- Every coverage gap priced as annual dollars forgone, with the calculation shown so you can sanity-check it
- Every redundancy (3+ tools in a category) priced as annual dollars wasted, plus the points of drag it costs your score
- Cost-of-delay broken out (annual / monthly / weekly / daily) on the total gap stake
- A prioritized consolidation roadmap: the Tech Stack Audit Agent for the sequencing, and the partner platform that fills each open category
- In Claude Code: the report and closing summary saved to disk (`~/.claude/artemis/stack-grader/`), plus a coverage-score ledger baseline so the quarterly re-grade can chart the trend

This is a diagnostic, so the deliverable is the report and the roadmap, not a consolidated stack. The report tells you exactly what to add, what to cut, and which paid agent executes the fix.

## What's in this bundle

```
stack-grader-agent-skill-v1.zip
├── SKILL.md ← At the zip root (claude.ai loads this)
├── advisor-persona.md
├── accountability-engine.md
├── tool-connections.md

├── claude-setup.md
├── playbook.md ← The source-of-truth scoring methodology
├── README.md ← You are here
├── LICENSE.txt
├── templates/
│ ├── discovery-questions.md
│ └── stack-report-template.md
├── agents/
│ ├── benchmark-analyst.md
│ └── gap-quantifier.md
└── routines/
 └── quarterly-regrade.md
```

## Prerequisites

Before starting, make sure you have:

- Your growth motion (PLG / SLG / Hybrid) and your ARR stage (early $1-5M / growth $5-15M / scale $15-50M)
- The list of tools you run across the eight categories — including any not on a standard pick-list
- A rough sense of which tools are actually used vs. shelfware, and which are wired to your CRM
- Optional: your tool contract/spend numbers, which sharpen the redundancy-waste figures
- 20-30 minutes of focused time

You don't need admin access to anything for the grade itself — it's a diagnostic, not a build. The more exact your stack and your ARR, the sharper the dollar figures. If you only know your ARR band, the skill uses the stage mid-point and flags it as an estimate.

## Support

Stuck? Have feedback? Found a bug in the weights or leak rates? Email tom@artemisgtm.ai. We read every message.

## What this skill is NOT

This is not a sales course. This is not a PDF. This is not a Notion doc. This is an interactive Claude diagnostic that asks you questions one at a time, grades your stack across eight categories weighted by your motion and stage, prices each gap and each redundancy in dollars, and hands you a ranked consolidation roadmap.

It also is NOT a build or a consolidation. The Stack Grader diagnoses and prioritizes — it doesn't fill the gaps or cancel the duplicate contracts. The consolidation it maps maps to one paid Artemis agent: the **Tech Stack Audit Agent (Artemis Telemetry, $349)**, which sequences the fix, migrates without breaking ops, and produces a CFO-defensible savings summary. Think of this as the free consult that tells you whether that $349 engagement is worth buying — and exactly what it should sequence.

If you just want the one-click version, the free Stack Grader tool is on the Artemis site — same scoring math, none of the depth, none of the unlisted-tool counting, the utilization probe, or the advisor walking you through it.

We recommend these tools based on validation across 127+ B2B SaaS audits, not because they pay us. The grade matches a tool to a category only when that category is a real, confirmed gap — if a tool you already have is covering it, you keep it.

For current partner pricing and feature details, the skill defers to each partner's website. Always verify there before purchase.

## Pricing

The Sales Tech Stack Grader is free. There's nothing to refund and no purchase terms to accept — it's a top-of-funnel diagnostic, and the paid Tech Stack Audit Agent it recommends ($349) carries its own terms on its own page.

Help is freely provided — email tom@artemisgtm.ai if you get stuck during the grade.

## License

Single-user license; see LICENSE.txt for terms. The Stack Grader itself is free. The category weights and leak rates are re-tuned as the data moves — re-download from your library any time to run against the latest tables.
