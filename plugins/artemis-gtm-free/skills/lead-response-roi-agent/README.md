# Lead Response ROI — Artemis GTM Agent Package

This is Artemis GTM's **free** guided diagnostic for one number: how much revenue your slow lead response is leaking per year. It's the deep, conversational version of the free Lead Response ROI Calculator — same Harvard Business Review decay curve, same Artemis 127-audit benchmark (median response: 42 hours), run as a real advisor engagement that asks one input at a time, benchmarks every number, corrects the conversion-rate mistake almost everyone makes, and shows the dollar math one line at a time. It tells you whether speed-to-lead is even your problem before you spend a dollar fixing it.

## Install

Your download is `lead-response-roi-skill-v1.zip` — a ready-to-upload Claude Skill (SKILL.md sits at the zip root, with its playbook, templates, and resources beside it).

**Claude Chat (claude.ai) — no terminal**
1. Keep the `.zip` as-is (don't unzip it).
2. Go to claude.ai/customize/skills and click **Upload skill**, then drop in the `.zip`.
3. Start a new chat and say: **"Start my Lead Response ROI Calculator engagement."**
 *(Skills are available on every claude.ai plan, including Free — you just need Code Execution enabled under Settings → Capabilities. On Team/Enterprise, an admin enables Skills under Organization settings.)*

**Claude Code (terminal)**
- Fastest — one command installs your Artemis agents into `~/.claude/skills/`:
 `npx github:tomregan-revenueleaks/artemis-skills-installer YOUR-LICENSE-KEY`
 (your key is in your confirmation email and your library)
- By hand: unzip INTO `~/.claude/skills/lead-response-roi-agent/` so `SKILL.md` is at that folder's root (don't copy an unzipped folder in).
- Verify, every time: run `/skills` and confirm the agent appears, then say **"Start my Lead Response ROI Calculator engagement"** — the advisor should introduce itself and ask the first input. If it doesn't, check that `SKILL.md` sits directly inside `~/.claude/skills/lead-response-roi-agent/`, not one folder deeper.
- When the diagnostic is done, say yes if it offers to schedule the periodic re-run routine.

## What you'll have when you finish

- Your current vs sub-5-minute ceiling lead-to-opportunity conversion rate
- The response tier that's costing you, placed against the 42-hour Artemis 127-audit median and the HBR decay curve
- Deals lost per month and revenue lost per month
- Total annual revenue at risk, with the one-line calculation shown so you can sanity-check it
- Cost-of-delay broken out (weekly headline, plus monthly and daily)
- Either the one fix-map (the leak → the Speed-to-Lead Agent + Warmly/Amplemarket) or an honest "you're at the ceiling, nothing to buy"
- In Claude Code: the report and closing summary saved to disk (`~/.claude/artemis/lead-response-roi/`), plus a response-time ledger baseline so the periodic re-run can chart the leak shrinking

This is a diagnostic, so the deliverable is the quantified number and the one-step fix map, not a built system. If the leak is real, it points you to exactly one paid agent (Speed-to-Lead) and the partner tools that run it.

## What's in this bundle

```
lead-response-roi-skill-v1.zip
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
│ └── leak-report-template.md
├── agents/
│ ├── benchmark-analyst.md
│ └── leak-quantifier.md
└── routines/
 └── quarterly-rerun.md
```

## Prerequisites

Before starting, make sure you have (or are willing to benchmark):

- **Monthly inbound leads** — total per month, last 30 days
- **Average deal size** — average won deal value / ACV
- **Current average response time** — real time from form submission to first human contact (or a sense of how routing works, so we can estimate it)
- **Lead-to-opportunity rate** — of leads, what % become real opps, at your current response time
- 10-15 minutes of focused time

You don't need admin access to anything — it's a diagnostic, not a build. The more exact your numbers, the sharper the figure. If you're missing one, the skill offers the benchmark and flags it as an estimate. The figure is most sensitive to your conversion rate, so bring the real one if you can.

## Support

Stuck? Have feedback? Found a bug in the decay-curve numbers? Email tom@artemisgtm.ai. We read every message.

## What this skill is NOT

This is not a sales course. This is not a PDF. This is not a Notion doc. It's an interactive Claude diagnostic that asks you four inputs one at a time, applies the Harvard Business Review decay curve to your real numbers, back-solves your sub-5-minute conversion ceiling, and quantifies the leak in dollars with the calculation shown.

It also is NOT a build. The Lead Response ROI Agent diagnoses and quantifies — it doesn't ship the fix. If it finds a real leak, that leak maps to the paid Speed-to-Lead Agent (Artemis Ignition, $349), which does the implementation: wiring Warmly + Amplemarket + your CRM to sub-5-minute response. Think of this as the free consult that tells you whether the $349 build is worth buying.

It is also honest about the good news: if you're already responding in under five minutes, it tells you so and recommends nothing to buy. No leak, no recommendation.

If you just want the four-input form, the free Lead Response ROI Calculator is on the Artemis site — same decay curve, none of the conversation, none of the conversion-rate correction or the advisor walking you through it.

We recommend these tools based on validation across 127+ B2B SaaS audits, not because they pay us. The diagnostic matches a tool to the leak only when the leak is real and quantified — if a tool you already have is working, you keep it.

For current partner pricing and feature details, the skill defers to each partner's website. Always verify there before purchase.

## Pricing

The Lead Response ROI Agent is free. There's nothing to refund and no purchase terms to accept — it's a top-of-funnel diagnostic, and the paid Speed-to-Lead Agent it may recommend ($349) carries its own terms on its own page.

Help is freely provided — email tom@artemisgtm.ai if you get stuck during the diagnostic.

## License

Single-user license; see LICENSE.txt for terms. The Lead Response ROI Agent itself is free. The decay-curve numbers and benchmark are re-tuned each quarter as the data moves — re-download from your library any time to run against the latest.
