# Recoverable Revenue ROI Diagnostic — Artemis GTM Agent Package

This is Artemis GTM's **free** guided ROI diagnostic: it puts one defensible dollar figure on how much revenue your go-to-market funnel is leaking, breaks it down by lever, and tells you which Artemis agent fixes the biggest one. It's the conversational, math-honest version of the free Sales ROI Calculator — same model, ten times the depth, run as a real advisor engagement. Crucially, it fixes the form's biggest flaw: it never double-counts the deals that flow through both your conversion and win-rate gaps, and it reports cycle as velocity instead of stacking it on as phantom ARR.

## Install

Your download is `roi-recovery-diagnostic-skill-v1.zip` — a ready-to-upload Claude Skill (SKILL.md sits at the zip root, with its playbook, templates, and resources beside it).

**Claude Chat (claude.ai) — no terminal**
1. Keep the `.zip` as-is (don't unzip it).
2. Go to claude.ai/customize/skills and click **Upload skill**, then drop in the `.zip`.
3. Start a new chat and say: **"Start my Sales ROI Calculator engagement."**
 *(Skills are available on every claude.ai plan, including Free — you just need Code Execution enabled under Settings → Capabilities. On Team/Enterprise, an admin enables Skills under Organization settings.)*

**Claude Code (terminal)**
- Fastest — one command installs your Artemis agents into `~/.claude/skills/`:
 `npx github:tomregan-revenueleaks/artemis-skills-installer YOUR-LICENSE-KEY`
 (your key is in your confirmation email and your library)
- By hand: unzip INTO `~/.claude/skills/roi-recovery-diagnostic-agent/` so `SKILL.md` is at that folder's root (don't copy an unzipped folder in).
- Verify, every time: run `/skills` and confirm the agent appears, then say **"Start my Sales ROI Calculator engagement"** — the advisor should introduce itself and ask the first funnel question. If it doesn't, check that `SKILL.md` sits directly inside `~/.claude/skills/roi-recovery-diagnostic-agent/`, not one folder deeper.
- When the diagnostic is done, say yes if it offers to schedule the quarterly re-run routine.

## What you'll have when you finish

- One defensible recoverable-revenue figure for your funnel — your annual won revenue at benchmark conversion and win rate, minus your current won revenue, with the double-counting stripped out
- The per-lever breakdown — how much of that figure is lead-to-opp conversion vs win rate, split so the two reconcile to the total
- The cycle-velocity line — how many days faster you'd close and the cash that pulls forward, kept separate from recoverable revenue (because a faster cycle realizes the same deals sooner, it doesn't make new ones)
- The gap named at BOTH the realistic median bar and the stretch top-quartile bar, so you have a number you can defend to finance, not just the aspirational one the form returns
- Cost-of-delay broken out (annual / monthly / weekly / daily)
- The single biggest lever mapped to the matching Artemis build agent (with a buyable link) and the recommended partner platform
- In Claude Code: the report and closing summary saved to disk (`~/.claude/artemis/roi-recovery-diagnostic/`), plus a recoverable-revenue ledger baseline so the quarterly re-run can chart the leak closing

This is a diagnostic, so the deliverable is the number and the fix-map, not a built system. The report tells you exactly which build agent your funnel math justifies and in what order.

## What's in this bundle

```
roi-recovery-diagnostic-skill-v1.zip
├── SKILL.md ← At the zip root (claude.ai loads this)
├── advisor-persona.md
├── accountability-engine.md
├── tool-connections.md

├── claude-setup.md
├── playbook.md ← The source-of-truth ROI methodology
├── README.md ← You are here
├── LICENSE.txt
├── templates/
│ ├── discovery-questions.md
│ └── roi-report-template.md
├── agents/
│ ├── benchmark-analyst.md
│ └── roi-quantifier.md
└── routines/
 └── quarterly-rerun.md
```

## Prerequisites

Before starting, make sure you have (or are willing to benchmark-fill) the seven funnel numbers the calculator uses:

- **Current ARR** — context only; sizes the opportunity
- **Average deal size** (ACV)
- **Monthly leads**
- **Lead-to-opportunity rate** (%)
- **Win rate** (%)
- **Sales cycle** (days)
- **Number of AEs**

You don't need admin access to anything — it's a diagnostic, not a build. The more exact your numbers, the sharper the dollar figure. If you're missing a number, the skill will offer the benchmark and flag it as an estimate, and a benchmark-filled field never becomes a gap you can leak against.

## Support

Stuck? Have feedback? Found a bug in the benchmark tables? Email tom@artemisgtm.ai. We read every message.

## What this skill is NOT

This is not a sales course. This is not a PDF. This is not a Notion doc. This is an interactive Claude diagnostic that asks you seven funnel questions one at a time, benchmarks your real numbers at both median and top-quartile, computes one internally-consistent recoverable-revenue figure (no double-counting), and hands you the biggest fix named.

It also is NOT a build. The ROI diagnostic quantifies and prioritizes — it doesn't ship the fix. Each lever it finds maps to a separate Artemis build agent (or the bundle) that does the implementation. Lead-to-opp gaps route to the Qualification Automation System ($349); win-rate gaps route to the Conversion Content System ($349). Think of this as the free consult that tells you which $349 agent your funnel math actually justifies, so you spend the build budget on the lever that actually costs you money.

If you just want the fast version, the free Sales ROI Calculator is on the Artemis site — same model, none of the depth, none of the median-bar honesty or the advisor walking you through the math.

We recommend these tools based on validation across 127+ B2B SaaS audits, not because they pay us. The diagnostic matches a tool to a lever only when that lever is a real, quantified gap — if a tool you already have is working, you keep it.

For current partner pricing and feature details, the skill defers to each partner's website. Always verify there before purchase.

## Pricing

The ROI diagnostic is free. There's nothing to refund and no purchase terms to accept — it's a top-of-funnel diagnostic, and the paid build agents it recommends ($349 each, or bundled) carry their own terms on their own pages.

Help is freely provided — email tom@artemisgtm.ai if you get stuck during the diagnostic.

## License

Single-user license; see LICENSE.txt for terms. The ROI diagnostic itself is free. The benchmark tables are re-tuned each quarter as the data moves — re-download from your library any time to run against the latest tables.
