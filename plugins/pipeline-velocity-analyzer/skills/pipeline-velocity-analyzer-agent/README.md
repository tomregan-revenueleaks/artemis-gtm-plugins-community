# Pipeline Velocity Analyzer — Artemis GTM Agent Package

This is Artemis GTM's **free** guided pipeline-velocity diagnostic: it computes how fast your pipeline moves in dollars per day, benchmarks it against your industry and growth motion, quantifies the dollar gap as a recoverable leak, and tells you which one of the four levers — leads, deal size, win rate, or cycle length — moves your revenue most. It's the deep, conversational version of the free Pipeline Velocity Calculator on the site — same formula, same benchmarks, run as a real advisor engagement that converts your raw leads into true opportunities, branches on your growth motion, and frames every lever in annual dollars.

## Install

Your download is `pipeline-velocity-analyzer-skill-v1.zip` — a ready-to-upload Claude Skill (SKILL.md sits at the zip root, with its playbook, templates, and resources beside it).

**Claude Chat (claude.ai) — no terminal**
1. Keep the `.zip` as-is (don't unzip it).
2. Go to claude.ai/customize/skills and click **Upload skill**, then drop in the `.zip`.
3. Start a new chat and say: **"Start my Pipeline Velocity Calculator engagement."**
 *(Skills are available on every claude.ai plan, including Free — you just need Code Execution enabled under Settings → Capabilities. On Team/Enterprise, an admin enables Skills under Organization settings.)*

**Claude Code (terminal)**
- Fastest — one command installs your Artemis agents into `~/.claude/skills/`:
 `npx github:tomregan-revenueleaks/artemis-skills-installer YOUR-LICENSE-KEY`
 (your key is in your confirmation email and your library)
- By hand: unzip INTO `~/.claude/skills/pipeline-velocity-analyzer-agent/` so `SKILL.md` is at that folder's root (don't copy an unzipped folder in).
- Verify, every time: run `/skills` and confirm the agent appears, then say **"Start my Pipeline Velocity Calculator engagement"** — the advisor should introduce itself and ask the first profile question. If it doesn't, check that `SKILL.md` sits directly inside `~/.claude/skills/pipeline-velocity-analyzer-agent/`, not one folder deeper.
- When the diagnostic is done, say yes if it offers to schedule the periodic velocity re-run routine.

## What you'll have when you finish

- Your pipeline velocity in dollars per day, with the math shown so you can sanity-check it
- The annualized pipeline revenue that velocity implies
- A letter grade (A-F) against your industry and growth motion (PLG / SLG / Hybrid)
- The annual dollar gap to benchmark — your velocity leak, quantified
- All four levers ranked by annual dollar impact, with the winning lever named and explained
- Cost-of-delay on the leak (annual / monthly / weekly / daily)
- A roadmap mapping the winning lever to the Artemis build agent that moves it and the recommended partner platform
- In Claude Code: the report and closing summary saved to disk (`~/.claude/artemis/pipeline-velocity/`), plus a velocity ledger baseline so the periodic re-run can chart the trend

This is a diagnostic, so the deliverable is the velocity read and the lever roadmap, not a built system. The report tells you exactly which lever to pull and which build agent fixes it.

## What's in this bundle

```
pipeline-velocity-analyzer-skill-v1.zip
├── SKILL.md ← At the zip root (claude.ai loads this)
├── advisor-persona.md
├── accountability-engine.md
├── tool-connections.md

├── claude-setup.md
├── playbook.md ← The source-of-truth velocity methodology
├── README.md ← You are here
├── LICENSE.txt
├── templates/
│ ├── discovery-questions.md
│ └── velocity-report-template.md
├── agents/
│ ├── benchmark-analyst.md
│ └── lever-quantifier.md
└── routines/
 └── velocity-rerun.md
```

## Prerequisites

Before starting, make sure you have:

- Your industry and growth motion (PLG / SLG / Hybrid)
- Monthly leads AND your lead-to-opportunity rate — the agent converts these into true opportunities, which keeps your velocity from being overstated
- Average deal size (ACV)
- Win rate (closed-won ÷ opportunities)
- Average sales cycle in days
- 15-25 minutes of focused time

You don't need admin access to anything — it's a diagnostic, not a build. The more exact your numbers, the sharper the velocity figure. If you're missing a number, the skill will offer the industry benchmark and flag it as an estimate.

## Support

Stuck? Have feedback? Found a bug in the benchmark tables? Email tom@artemisgtm.ai. We read every message.

## What this skill is NOT

This is not a sales course. This is not a PDF. This is not a Notion doc. This is an interactive Claude diagnostic that asks you for your numbers one at a time, converts your leads into true opportunities, computes your velocity in dollars per day, benchmarks it against your industry and motion, quantifies the leak in dollars, and tells you which single lever to pull.

It also is NOT a build. The Pipeline Velocity Analyzer diagnoses and prioritizes — it doesn't ship the fix. The winning lever maps to a separate Artemis build agent (most often the Conversion Content System, which moves the win-rate and deal-size levers) that does the implementation. Think of this as the free consult that tells you which $349 agent is worth buying for the lever that actually moves your revenue.

If you just want a fast self-serve number, the Pipeline Velocity Calculator is on the Artemis site — same formula, but it assumes your leads equal opportunities and applies a flat 25% improvement to every lever. This agent fixes both, and frames every lever in annual dollars.

We recommend these tools based on validation across 127+ B2B SaaS audits, not because they pay us. The diagnostic matches a tool to a lever only when that lever is real and below benchmark — if a tool you already have is working, you keep it.

For current partner pricing and feature details, the skill defers to each partner's website. Always verify there before purchase.

## Pricing

The Pipeline Velocity Analyzer is free. There's nothing to refund and no purchase terms to accept — it's a top-of-funnel diagnostic, and the paid build agents it recommends ($349 each, or bundled) carry their own terms on their own pages.

Help is freely provided — email tom@artemisgtm.ai if you get stuck during the diagnostic.

## License

Single-user license; see LICENSE.txt for terms. The Pipeline Velocity Analyzer itself is free. The benchmark tables are re-tuned each quarter as the data moves — re-download from your library any time to run against the latest tables.
