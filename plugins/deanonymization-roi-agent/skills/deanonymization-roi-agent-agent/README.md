# Website Visitor ROI Calculator — Artemis GTM Agent Package

This is Artemis GTM's **free** guided diagnostic: it quantifies exactly how much pipeline is hiding in the anonymous traffic on your website — the 98% who research and leave without ever filling a form — in real dollars per year, and tells you whether visitor identification is worth installing for YOUR traffic. It's the deep version of the free Website Visitor ROI Calculator on the site — same funnel math, run as a real advisor engagement that asks one input at a time, branches on your traffic mix, and separates the upper bound from the expected figure so the number survives a CFO's pushback.

## Install

Your download is `deanonymization-roi-agent-skill-v1.zip` — a ready-to-upload Claude Skill (SKILL.md sits at the zip root, with its playbook, templates, and resources beside it).

**Claude Chat (claude.ai) — no terminal**
1. Keep the `.zip` as-is (don't unzip it).
2. Go to claude.ai/customize/skills and click **Upload skill**, then drop in the `.zip`.
3. Start a new chat and say: **"Start my Website Visitor ROI Calculator engagement."**
 *(Skills are available on every claude.ai plan, including Free — you just need Code Execution enabled under Settings → Capabilities. On Team/Enterprise, an admin enables Skills under Organization settings.)*

**Claude Code (terminal)**
- Fastest — one command installs your Artemis agents into `~/.claude/skills/`:
 `npx github:tomregan-revenueleaks/artemis-skills-installer YOUR-LICENSE-KEY`
 (your key is in your confirmation email and your library)
- By hand: unzip INTO `~/.claude/skills/deanonymization-roi-agent-agent/` so `SKILL.md` is at that folder's root (don't copy an unzipped folder in).
- Verify, every time: run `/skills` and confirm the agent appears, then say **"Start my Website Visitor ROI Calculator engagement"** — the advisor should introduce itself and ask the first traffic question. If it doesn't, check that `SKILL.md` sits directly inside `~/.claude/skills/deanonymization-roi-agent-agent/`, not one folder deeper.
- When the run is done, say yes if it offers to schedule the quarterly re-run routine.

## What you'll have when you finish

- An expected annual pipeline figure hiding in your anonymous traffic, with the funnel calculation shown so you can sanity-check it
- The upper-bound figure alongside it, so you have a defensible range — not a single fragile point estimate
- The identification-rate benchmark used, tied to your actual traffic mix (and which tool that mix points to)
- Cost-of-delay broken out (annual / monthly / weekly / daily) on the expected figure
- A clear "pipeline created, not closed revenue" caveat, with the win-rate multiplier you'd apply for closed-won
- The fix: the Visitor Deanonymization build agent (with a buyable link) and the recommended partner tool for your traffic
- In Claude Code: the report and closing summary saved to disk (`~/.claude/artemis/deanonymization-roi-agent/`), plus a pipeline ledger baseline so the quarterly re-run can chart the trend as your traffic grows

This is a diagnostic, so the deliverable is the figure and the roadmap, not a built system. The number tells you whether identification is worth installing for your traffic, and the roadmap names the agent and tool that capture it.

## What's in this bundle

```
deanonymization-roi-agent-skill-v1.zip
├── SKILL.md ← At the zip root (claude.ai loads this)
├── advisor-persona.md
├── accountability-engine.md
├── tool-connections.md

├── claude-setup.md
├── playbook.md ← The source-of-truth funnel methodology
├── README.md ← You are here
├── LICENSE.txt
├── templates/
│ ├── discovery-questions.md
│ └── leak-report-template.md
├── agents/
│ ├── benchmark-analyst.md
│ └── pipeline-quantifier.md
└── routines/
 └── quarterly-rerun.md
```

## Prerequisites

Before starting, make sure you have:

- Your monthly website visitors (from Google Analytics or your analytics tool) — the top of the funnel
- Your average deal size / ACV (from the CRM) — the anchor on every dollar figure
- A rough sense of your traffic mix: US vs international, paid vs organic, how much is plausibly ICP-fit
- Optional, to sharpen the figure: your real current form-fill rate and your real meeting-to-opportunity rate
- 15-30 minutes of focused time

You don't need a visitor-identification tool installed for this — in fact the most common reason to run it is to decide whether one is worth buying. The more exact your visitor count and deal size, the sharper the figure. If you're missing a number, the skill will offer the benchmark and flag it as an estimate.

## Support

Stuck? Have feedback? Found a number that looks off? Email tom@artemisgtm.ai. We read every message.

## What this skill is NOT

This is not a sales course. This is not a PDF. This is not a Notion doc. This is an interactive Claude diagnostic that asks you for your traffic and deal size one input at a time, runs an honest identification-to-pipeline funnel, separates the upper bound from the expected figure, and hands you a defensible dollar number plus the one fix that captures it.

It also is NOT a build. The Website Visitor ROI Calculator quantifies and prioritizes — it doesn't install the tool. The anonymous-traffic leak it sizes maps to a separate Artemis build agent — Visitor Deanonymization (Artemis Beacon, $349) — that does the implementation: picks Warmly vs RB2B vs hybrid for your traffic, installs the script, tunes ICP filters, and wires the CRM + Slack workflow. Think of this as the free consult that tells you whether that $349 agent is worth buying for your traffic, so you spend the build budget only when the pipeline is actually there.

It also is NOT the whole-funnel GTM Audit. This calculator measures one leak — anonymous visitors. If your real problem is somewhere else (slow response, no scoring, weak conversion pages), the skill will flag it and point you at the free GTM Audit, which sizes leaks across the whole go-to-market.

If you just want the fast version, the free Website Visitor ROI Calculator is on the Artemis site — same funnel, two inputs, one number, none of the traffic-mix branching, the upper-bound band, or the advisor walking you through it.

We recommend these tools based on validation across 127+ B2B SaaS audits, not because they pay us. The calculator matches a tool to your traffic profile only when the anonymous-traffic leak is real and quantified — if your traffic is too thin to justify a tool, it tells you to grow traffic first, and recommends nothing.

For current partner pricing, match rates, and feature details, the skill defers to each partner's website. Always verify there before purchase.

## Pricing

The Website Visitor ROI Calculator is free. There's nothing to refund and no purchase terms to accept — it's a top-of-funnel diagnostic, and the paid build agent it recommends ($349) carries its own terms on its own page.

Help is freely provided — email tom@artemisgtm.ai if you get stuck during the run.

## License

Single-user license; see LICENSE.txt for terms. The Website Visitor ROI Calculator itself is free. The benchmark tables are re-tuned each quarter as the data moves — re-download from your library any time to run against the latest tables.
