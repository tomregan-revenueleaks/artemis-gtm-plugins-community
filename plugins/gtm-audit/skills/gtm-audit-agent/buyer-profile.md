# Buyer Profile, the cross-agent identity layer

Load this right after `accountability-engine.md`. The accountability engine remembers
THIS engagement (the build, the results, the gaps). The buyer profile remembers the
BUYER across every Artemis agent they run, so the second agent they buy does not make
them re-explain who they are.

## What this is

A buyer who owns more than one Artemis agent should never re-run Discovery from a cold
start. Their company, their Pain-First ICP, their stack, their connected tools, their
stage, and the decisions they have already made are the same whichever agent is running.
The buyer profile is the one durable place those agent-agnostic facts live, written once
and read first by every agent.

Keep it strictly separate from per-engagement state. `build-state.json`,
`results-ledger`, and `observations` live in the per-agent dir and belong to ONE agent.
The profile holds only what is true about the buyer no matter which agent is running.
When in doubt: if a fact would be identical in a different agent's engagement, it belongs
in the profile; if it is about this specific build, it stays in the per-agent dir.

## The file, per surface

- **Claude Code:** `~/.claude/artemis/_profile/profile.json`, one level ABOVE the
 per-agent dirs, so every agent on this machine shares it. It survives reboots and skill
 reinstalls like the rest of `~/.claude/artemis/`.
- **Codex:** `./.artemis/_profile/profile.json` in the project working dir. Same model as
 the per-agent Codex state: it is shared across agents the buyer runs from this project
 dir. If the buyer runs agents from different project dirs, tell them to keep their GTM
 work in one dir so the profile follows.
- **claude.ai (Chat):** a Project file named `GTM-PROFILE.md`. Projects do not share files,
 so this works fully only when the buyer keeps all their Artemis agents in ONE Project
 (recommend "Artemis GTM"). Say that plainly; on Chat the cross-agent win depends on it.

## What the profile holds

```json
{
 "schema_version": 1,
 "company": "<name>", "domain": "<domain>",
 "surface": "claude-code|codex|chat", "plan": "<plan>",
 "pain_first_icp": "<the ICP, in the buyer's own framing>",
 "stack": ["<tool>", "..."],
 "connectors": { "hubspot": false, "salesforce": false, "slack": false, "calendar": false, "gmail": false, "amplemarket": false, "warmly": false },
 "stage": "<seed|growth|scale|...>", "team_size": "<n or range>",
 "nudge_intensity": "gentle|active",
 "owned": ["<slug>", "..."],
 "shipped": [ { "slug": "<slug>", "date": "<date>", "outcome": "<one line of what it produced>",
 "artifacts": { "<logical-name>": "<path to the real deliverable>" } } ],
 "decisions": [ { "topic": "<what was decided>", "chosen": "<path taken>", "rejected": ["<alternative>", "..."], "reason": "<one line>", "date": "<date>" } ],
 "voice": { "samples": ["<short real sample>", "..."], "required": ["<phrasing to keep>"], "banned": ["<phrasing to avoid>"], "reading_level": "<plain|technical>", "pov": "<first-person plural, etc>", "cta_style": "<soft|direct>", "do_not_say": ["<term>"] }
}
```

Every field is optional and grows over time. A first-ever engagement starts an empty
profile and fills what Discovery surfaces.

## The loop

1. **Read first (kickoff, before any Discovery question).** Per `accountability-engine.md`
 section 1, load the profile before you ask anything. Pre-fill what it already knows and
 CONFIRM rather than re-ask: "Last time we had you as [company], selling to [ICP], on
 HubSpot. Still right?" Then run only the delta: the questions THIS agent needs that the
 profile does not already answer. If there is no profile yet, create it from this
 Discovery.

2. **Honor decisions and ownership (whenever you recommend).** Before you surface a
 cross-recommendation, name a next agent, or pitch a tool, read `owned`, `shipped`, and
 `decisions`. Never recommend an agent the buyer already owns or shipped. Never re-pitch
 a tool or path they already declined (it is in `decisions[].rejected`); if their
 situation has genuinely changed, you may revisit it once, naming the change. This is the
 inverse of "pairs well": recommend only when it pairs well AND they have not already
 said no.

2a. **Reuse what they already built (before any starter path).** When this agent needs the
 output of a prerequisite agent (Lead Scoring needs a Pain-First ICP, Outbound needs an
 ICP), check `shipped[].artifacts` for the real deliverable the buyer already paid to
 build. If it is there, read it and confirm-reuse it ("you built a 7-dimension ICP
 scorecard with ICP Definition, I'll anchor the fit model to that, not rebuild a generic
 one"), do NOT run the template starter. A buyer who paid for the prerequisite should never
 watch you rebuild a worse version of it. Graceful-degrade to the starter only if the
 artifact is genuinely missing or unreadable (a different machine, or claude.ai).

3. **Use the voice (copy agents).** Any agent that drafts buyer-facing copy (sequences,
 pillars, nurture, conversion content) reads `voice` before drafting and stays on it.
 The first copy agent that has no `voice` yet captures it (paste-back: one or two real
 samples, the do-not-say list), writes it to the profile, and every later copy agent
 inherits it. Results learnings about what voice won compound here too (see
 `results-memory.md`).

4. **Write back (session close and SHIPPED).** Write durable facts back as they firm up
 (a corrected ICP, a newly connected tool, a decision and its rejected alternatives). At
 the SHIPPED moment, append this agent's slug and a one-line outcome to `shipped`, add the
 slug to `owned`, AND record the paths to this agent's key deliverables in that shipped
 row's `artifacts` map (a logical name to the real file, e.g. `"icp-scorecard":
 "~/.claude/artemis/gtm-engineer/engagements/icp/artifacts/icp-scorecard.md"` under the
 shell, or the legacy per-slug path
 `~/.claude/artemis/icp-definition/artifacts/icp-scorecard.md` on a standalone install) so
 the next agent that needs them can reuse them (step 2a). This is a memory write, never a
 live-system change.

## Posture

Confirm before overwriting a known fact ("I have your ICP as X, update it?"), do not
silently clobber. The profile is the buyer's, not yours. On claude.ai, where you cannot
read the Project file programmatically in every case, reconstruct from the conversation and
ask the buyer to keep `GTM-PROFILE.md` current, the same graceful degradation the build
tracker uses. Never make the buyer re-explain from scratch what a prior agent already knew.
