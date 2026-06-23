# Getting the best out of your Artemis agent — a 60-second setup

*Verified June 2026. Claude's settings change often — if a label here doesn't match
what's on your screen, trust the screen (the live source is support.claude.com), and
I can check the current setting with you.*

You've hired an advisor. Before we dig in, let's make sure Claude itself is set up to
give you its sharpest work — the difference between a fast answer and a thorough one.
This takes about a minute. If you've done it before, just say "skip" and we'll get
straight to work.

One question changes everything below: **are you on Claude on the web (claude.ai),
or Claude Code in a terminal?** Read the matching section.

---

## On Claude (web or desktop app) — most people

**1. Work inside a Project, with your real data in it.**
This is the single biggest lever. A Project is a dedicated workspace that holds your
files and remembers context across our sessions. Go to **Projects → New Project**,
name it (e.g. "Artemis — Lead Scoring build"), and drag your real material into the
knowledge area: a CRM export, your current sequences, your routing rules. Now I'm
working *from* your business instead of guessing about it. In the Project's custom
instructions, add: *"Load and follow this agent. My CRM is [X], my ICP is [Y]."*

**2. Use the most capable model for the thinking-heavy parts.**
Click the model name next to the send button and choose the **most capable option**
(today that's the top Opus). Leave the effort/quality setting at its default — it
already reasons deeply on hard steps, so you don't need to hunt for an "extended
thinking" switch. Use this model while we diagnose your situation and build strategy.
For fast back-and-forth drafting later, a quicker model is fine and saves your usage —
I'll tell you when to switch.

**3. Turn on web search when we need live facts.**
When we look up a tool's current pricing or features, switch on **Web search** for
that chat so I cite live sources instead of guessing. *(On Team/Enterprise an admin
may need to enable it first.)*

**4. Bring your files in.**
Use the **+** button to add files — PDFs, DOCX, spreadsheets, CSVs. The more of your
real data I can see, the sharper the build.

**5. New chat per phase — your context follows you.**
Memory is **on by default** and carries your context across chats, and your Project
holds the shared knowledge. So when we move from diagnosis to building, start a
**fresh chat inside the same Project**. You keep all the context without dragging a
giant transcript along, which keeps my answers crisp. *(To manage memory, use
**Pause**, never **Reset** — Reset permanently deletes everything, including your
Project memories.)*

---

## On Claude Code (terminal) — for the technical

**0. Installing (or re-installing) the agent.** The download is a flat zip:
`SKILL.md` sits at the zip root with its resources beside it. Unzip it **into** the
skill folder — don't copy an unzipped folder in:

```bash
mkdir -p ~/.claude/skills/<agent-name>-agent
unzip <your-download>.zip -d ~/.claude/skills/<agent-name>-agent
```

Then verify, every time:

1. Run `/skills` in Claude Code. The agent's name appears in the list.
2. Say the kickoff phrase — **"Start my [Agent Display Name] engagement"** (for
 example, "Start my GTM Audit engagement"). The advisor introduces itself and
 asks the first discovery question.

If step 2 fails, check that `SKILL.md` sits directly inside
`~/.claude/skills/<agent-name>-agent/` — not one folder deeper. If you see
`~/.claude/skills/<agent-name>-agent/<something>/SKILL.md`, move everything up one
level and run `/skills` again.

**1. Update first:** run `claude update`. A couple of the steps below need a recent
version.

**2. One command sets the model for the whole engagement: `/model opusplan`.**
This uses the most capable model while planning (your diagnosis and strategy) and
automatically switches to a faster model while building. On a **Pro** (or Team
Standard) plan your default is the faster model, so you do need to opt in here; on
**Max / Team Premium** you're already on the top model. For a single unusually hard
step, type the word `ultrathink` anywhere in your prompt.

**3. Memory is on by default.** I'll remember your answers and decisions across
sessions. Optional: drop your company facts into a `CLAUDE.md` file in your project
folder so they load every session.

**4. Your connected tools come with you.** Connectors you added at claude.ai
(HubSpot, Slack, Gmail) load automatically here when you're signed in with the same
account — nothing extra to do. If not, run `claude mcp add ...` then `/mcp`.

**5. If my instructions seem to fade** in a long session (after a `/compact`), just
type `/<agent-name>` again to reload the full playbook.

---

## Plan requirements

- **Running this agent requires a paid Claude plan — Pro or above.** Claude Code,
 where the agent installs as a skill, is a paid-plan product. Pro is also where the
 most capable models with full reasoning, smarter Project knowledge, and
 past-chat-searching memory live — the levers most of this guide is about. (If
 Claude Code runs on your machine at all, your plan qualifies; a `/skills` no-show
 is a folder-layout issue, not a plan issue.)
- **On claude.ai**, running the agent as a custom skill additionally needs
 **Code execution and file creation** and **Skills** turned on
 (Settings → Capabilities; on Team/Enterprise an admin enables these under
 Organization settings → Skills).
- **Max / Team Premium:** the top model by default, plus the usage headroom for
 long build sessions. The right call once the agent is part of your weekly
 operating rhythm.

---

## The 30-second version — which model, by phase

| Phase | What we're doing | Claude (web) | Claude Code |
|---|---|---|---|
| **Diagnose** | I read your situation, find the leaks | Most capable (Opus) | `/model opusplan` |
| **Strategy** | We decide the plan | Most capable (Opus) | (opusplan: still planning) |
| **Build** | Draft sequences, scores, playbooks | A faster model is fine | (opusplan switches for you) |
| **Iterate** | "Make it punchier", variants | Faster model | Faster model |
| **Verify** | Fresh chat, check the build | Most capable | Default |
| **Support** | Quick follow-ups later | Faster model | Default |
