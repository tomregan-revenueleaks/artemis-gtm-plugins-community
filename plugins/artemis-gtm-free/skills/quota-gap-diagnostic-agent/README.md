# Quota Gap Diagnostic — Artemis GTM Agent Package

This is Artemis GTM's **free** guided diagnostic for one question every sales leader needs answered before the quarter is decided: **can you hit your number, and if not, what's the one thing to fix?** It works backward from your revenue target to the pipeline you actually need, sizes the true gap in dollars (after crediting the pipeline you already have), grades your coverage against what your win rate really requires, checks whether your AEs can carry the load, and diagnoses whether the gap is a volume, conversion, or capacity problem — then names the single Artemis agent that closes it. It's the deep, conversational version of the free Quota Attainment Gap Analyzer — same math, the follow-ups a form can't ask, run as a real advisor engagement.

## Install

Your download is `quota-gap-diagnostic-skill-v1.zip` — a ready-to-upload Claude Skill (SKILL.md sits at the zip root, with its playbook, templates, and resources beside it).

**Claude Chat (claude.ai) — no terminal**
1. Keep the `.zip` as-is (don't unzip it).
2. Go to claude.ai/customize/skills and click **Upload skill**, then drop in the `.zip`.
3. Start a new chat and say: **"Start my Quota Attainment Gap Analyzer engagement."**
 *(Skills are available on every claude.ai plan, including Free — you just need Code Execution enabled under Settings → Capabilities. On Team/Enterprise, an admin enables Skills under Organization settings.)*

**Claude Code (terminal)**
- Fastest — one command installs your Artemis agents into `~/.claude/skills/`:
 `npx github:tomregan-revenueleaks/artemis-skills-installer YOUR-LICENSE-KEY`
 (your key is in your confirmation email and your library)
- By hand: unzip INTO `~/.claude/skills/quota-gap-diagnostic-agent/` so `SKILL.md` is at that folder's root (don't copy an unzipped folder in).
- Verify, every time: run `/skills` and confirm the agent appears, then say **"Start my Quota Attainment Gap Analyzer engagement"** — the advisor should introduce itself and ask the first input. If it doesn't, check that `SKILL.md` sits directly inside `~/.claude/skills/quota-gap-diagnostic-agent/`, not one folder deeper.
- When the diagnostic is done, say yes if it offers to schedule the quarterly coverage re-check routine.

## What you'll have when you finish

- Your real pipeline coverage ratio and its letter grade (A-F), graded against what your win rate actually requires (1 ÷ win rate), not a static 3-4x rule of thumb
- The dollar size of your true quarterly pipeline gap — net of the pipeline you already have, with the calculation shown so you can sanity-check it
- The incremental leads, opportunities, and deals per month it takes to close the gap, off your real lead-to-opp rate (not a hardcoded 25%)
- An AE-capacity verdict — active opps per rep against the 25-opp ceiling where win rates start dropping
- A time-to-close cutoff date — the last day a new deal can be created and still close this quarter
- A single diagnosis — volume, conversion, or capacity (or a clean "on track") — with the reasoning
- Cost-of-delay broken out (annual / monthly / weekly / daily) on the gap
- The one Artemis build agent that closes the diagnosed problem (with a buyable link) and the partner platform under it
- In Claude Code: the report and closing summary saved to disk (`~/.claude/artemis/quota-gap-diagnostic/`), plus a coverage ledger baseline so the quarterly re-check can chart the trend

This is a diagnostic, so the deliverable is the report and the one recommendation, not a built system. The report tells you whether you'll make plan and exactly which build agent to buy if you won't.

## What's in this bundle

```
quota-gap-diagnostic-skill-v1.zip
├── SKILL.md ← At the zip root (claude.ai loads this)
├── advisor-persona.md
├── accountability-engine.md
├── tool-connections.md

├── claude-setup.md
├── playbook.md ← The source-of-truth diagnostic methodology
├── README.md ← You are here
├── LICENSE.txt
├── templates/
│ ├── discovery-questions.md
│ └── gap-report-template.md
├── agents/
│ ├── coverage-benchmark-analyst.md
│ └── gap-quantifier.md
└── routines/
 └── quarterly-recheck.md
```

## Prerequisites

Before starting, make sure you have:

- Your core numbers, or a willingness to use benchmarks as placeholders — annual (or quarterly) revenue target, number of AEs, average deal size, win rate, and average sales cycle
- Your current open pipeline value — and whether it's raw or win-rate-weighted
- Optional but sharper: your real lead-to-opp rate (the tool defaults to 25% if you don't have it) and the days left in your quarter (defaults to today's calendar)
- 15-25 minutes of focused time

You don't need admin access to anything for the diagnostic itself — it's a diagnostic, not a build. The more exact your numbers, the sharper the gap figure. If you're missing a number, the skill will offer the benchmark and flag it as an estimate. Note that required pipeline chains off your win rate, so a benchmark-filled win rate makes the gap directional rather than precise.

## Support

Stuck? Have feedback? Found a bug in the benchmark tables? Email tom@artemisgtm.ai. We read every message.

## What this skill is NOT

This is not a sales course. This is not a PDF. This is not a Notion doc. This is an interactive Claude diagnostic that asks you for your numbers one at a time, works backward from your target to the pipeline you need, nets out the pipeline you already have, benchmarks your coverage against what your win rate requires, and hands you a single diagnosis and the one build that fixes it.

It also is NOT a build. The Quota Gap Diagnostic diagnoses — it doesn't ship the fix. The problem it finds maps to a separate Artemis build agent that does the implementation: a volume gap to the Outbound System (Artemis Hunter), a conversion gap to the Conversion Content System (Artemis Lander), a capacity gap to Qualification Automation (Artemis Gateway). Think of this as the free read that tells you whether you'll make plan and which $349 agent is worth buying if you won't.

If you just want the fast self-serve version, the free Quota Attainment Gap Analyzer is on the Artemis site — same core formulas, but it doesn't subtract the pipeline you already have, grades coverage against a flat band, and assumes a hardcoded 25% lead-to-opp rate. The agent fixes all three and walks you through it.

We recommend these tools based on validation across 127+ B2B SaaS audits, not because they pay us. The diagnostic matches a tool to your diagnosed problem only when that problem is real and quantified — if a tool you already have is working, you keep it.

For current partner pricing and feature details, the skill defers to each partner's website. Always verify there before purchase.

## Pricing

The Quota Gap Diagnostic is free. There's nothing to refund and no purchase terms to accept — it's a top-of-funnel diagnostic, and the paid build agents it recommends ($349 each) carry their own terms on their own pages.

Help is freely provided — email tom@artemisgtm.ai if you get stuck during the diagnostic.

## License

Single-user license; see LICENSE.txt for terms. The Quota Gap Diagnostic itself is free. The benchmark tables are re-tuned each quarter as the data moves — re-download from your library any time to run against the latest tables.
