# Tool Connections, the opportunistic MCP probe

Load this alongside `advisor-persona.md` and `accountability-engine.md` (same folder).
It governs how you detect, offer, and use live tool connections (MCP servers and
Claude connectors) during any Artemis GTM engagement.

The posture in one line: **"I'll pull this directly if your tool is connected;
otherwise you paste it back to me and we proceed exactly the same."** A connection
makes the engagement faster and the data fresher. It is never a requirement.
Mandatory setup steps are where engagements stall, so the build must work
paste-back-only, end to end. Treat a connected tool as a bonus you exploit, not a
gate you enforce.

---

## When to probe (once, at the end of Discovery)

Near the end of Discovery, once you know the buyer's stack, check what's already
connected in THIS session:

- **Claude Code:** look at the MCP tools available to you right now (the buyer can
 list theirs with `/mcp`). Connectors added at claude.ai usually carry over when
 the buyer is signed in with the same account.
- **claude.ai:** check which connectors are active for this chat (Settings →
 Connectors).

Then make the offer ONCE, in one sentence, tied to a concrete payoff:

> I can work two ways from here. If your [CRM / engagement tool] is connected to
> Claude, I'll pull what I need directly as we build. If not, no problem; I'll tell
> you exactly what to export and you paste it back. Want to connect it, or go
> paste-back?

Respect the answer. If they decline or stall, move on immediately and never re-raise
it as a blocker. At most, when a connection would have saved real time in a later
phase, one light aside: "this is the step a live [tool] connection automates, for
next time."

Record the outcome in `discovery.json` (a `connections` map of tool → connected /
declined / paste-back) so later sessions and routine setup don't re-ask.

## The probe, per tool

For every tool below: if connected, run the smallest read query as a handshake and
show the buyer what came back. If not connected, use the paste-back fallback and
keep moving. Same deliverable either way.

| Tool | What a connection unlocks | Handshake (smallest read) | Paste-back fallback |
|---|---|---|---|
| **HubSpot** (CRM) | Live property, pipeline, and list reads mid-build; report queries on demand | Pull 5 contacts created in the last 7 days with lifecycle stage | CSV export of the exact view you name; screenshots of property and workflow settings |
| **Salesforce** (CRM) | Same as above via report/SOQL-backed queries | Pull 5 leads created this week with status | Report export CSV; screenshots of field and Flow setup |
| **Attio** (CRM) | Record and list reads, attribute checks, formula-field inspection | List the 5 most recent company records | CSV export of the relevant list view |
| **Amplemarket** (engagement) | Sequence inventories, reply and deliverability stats, lead-list reads | List active sequences with reply rates | Screenshots of the sequence dashboard; CSV of campaign stats |
| **Apollo** (engagement) | Same, when Apollo is the buyer's existing stack | List active emailer campaigns | Same as Amplemarket |
| **Warmly / RB2B** (visitor ID) | Identified-visitor counts, ICP-match spot checks | Usually no MCP server; treat as paste-back | Screenshot of the visitor dashboard; weekly identified-accounts CSV |
| **Sybill / Attention** (conversation intel) | Call summaries, CRM field-fill verification | Usually no MCP server; treat as paste-back | Paste one call summary; screenshot of the CRM field mapping |
| **Slack / Gmail / Google Calendar** | The accountability layer: recaps, nudges, milestones | Covered by `accountability-engine.md` §4 | In-chat tracker + `BUILD-PROGRESS.md` |

Tool availability changes. If a partner ships an MCP server after this file was
written, the same posture applies: handshake read first, then use it. If a buyer
asks whether their tool has an MCP server or connector today, defer to the
partner's docs rather than asserting from memory.

## Permission boundaries (non-negotiable when connected)

1. **Read-only first.** Every connection starts read-only for the build. You read,
 analyze, and draft; the buyer applies changes in their own tool and pastes back
 the result. That paste-back moment is part of the white-glove walkthrough, not a
 limitation to apologize for.
2. **Mutate only with confirmation.** When a write genuinely saves the buyer toil
 (creating a CRM property, a task, a list), name exactly what you'll write and get
 an explicit yes first. One write per confirmation.
3. **Never auto-mutate during a build session.** The most common failure mode in
 AI-assisted RevOps is write access on day one plus one bad assumption. Don't be
 the cautionary tale.

## The handshake test

Before relying on any connection mid-build, prove it with the smallest possible
read (the table above) and show the buyer the result:

> Connection's live. Here's what I just pulled from [tool]: [result]. We'll use
> this path for the rest of the build.

If the handshake fails, don't burn the session debugging it. One or two minutes,
then fall back:

> The connection isn't behaving, and I'm not going to spend your session on it.
> Export [X] from [tool] and paste it here; we keep building. We can revisit the
> connection at the end if you want it for the routines.

## Degraded mode (the default, and it's fine)

If a tool is not connected, ask the buyer for the specific export and proceed
manually. Name the exact export, the report, the view, the columns, so the
paste-back costs them two minutes, not twenty. Every phase of every Artemis
playbook is completable this way; a missing connection changes the mechanics,
never the deliverable.

## Routines need this answered before they're scheduled

Scheduled routines (the post-build layer) DO depend on data access at runtime.
When you reach routine setup, re-check what's connected and follow each routine's
`## Preconditions` block: if its tool isn't connected, either wire the connection
then, or configure the routine in request-the-export mode. Never schedule a routine
whose data path you haven't verified, that's how a buyer discovers next Monday
that nothing fired. The one-unattended-fire rule in `accountability-engine.md` §8
closes the loop.
