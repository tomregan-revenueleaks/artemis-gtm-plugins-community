# Write Guard, enforcement for the destructive class (optional, defense in depth)

The connector-action fences in `connector-actions.md` are instructions the agent follows:
confirm before writing, one field at a time, no deletes, no bulk, and no `crm.write` from an
unattended routine. Those are strong, but they are prose. This file gives you a small
enforcement layer that turns the most dangerous part of that discipline from "the agent was
told not to" into "the tool call cannot happen," by hard-blocking destructive operations
before they reach your connected tools.

It is optional and it is Claude Code only. If you connect a CRM and let an agent write to it,
installing this is the single highest-value safety step you can take. Offer it to the buyer
when `crm.write` is enabled; do not force it.

## What it does, and its honest limits

- **Blocks the destructive class.** A Claude Code PreToolUse hook inspects every MCP tool call
 and hard-blocks any whose name signals a high-blast-radius operation (delete, destroy, purge,
 wipe, bulk, batch, merge, deprovision) before it runs. This is the class that turns a single
 wrong step into an incident, and it is blocked even if the model is steered by injected
 content in a CRM note or an inbound email.
- **It is a floor, not the whole discipline.** It does not enforce "one field at a time" or
 "confirm first" (those stay the agent's job and yours), and it recognizes danger by tool
 name, so a destructively-named-differently tool could slip past. Treat it as a backstop that
 removes the catastrophic failures, on top of the confirm-before-write discipline, not as a
 replacement for reviewing what the agent does.
- **Surface scope.** Claude Code only (it uses Claude Code hooks). Codex relies on its own
 approval and sandbox model plus the prose fences; claude.ai has no hook layer, so there the
 human confirmation on every write is the control. Say this plainly, do not imply the guard
 protects Codex or Chat.
- **Fail-open by design.** If the hook cannot parse a tool call it lets it through, so a format
 change never breaks the buyer's workflow. It is a safety net, not a gate you bet the business
 on.

## Install (about two minutes)

1. Save the script below to `~/.claude/hooks/artemis-write-guard.sh` and make it executable:

 ```bash
 mkdir -p ~/.claude/hooks
 # paste the script into ~/.claude/hooks/artemis-write-guard.sh, then:
 chmod +x ~/.claude/hooks/artemis-write-guard.sh
 ```

2. Add the hook to your Claude Code settings (`~/.claude/settings.json`), merging into any
 existing `hooks` block:

 ```json
 {
 "hooks": {
 "PreToolUse": [
 {
 "matcher": "mcp__.*",
 "hooks": [
 { "type": "command", "command": "~/.claude/hooks/artemis-write-guard.sh", "timeout": 15 }
 ]
 }
 ]
 }
 }
 ```

3. The script:

 ```bash
 #!/usr/bin/env bash
 # Artemis GTM write guard, a Claude Code PreToolUse hook.
 # Hard-blocks destructive MCP operations (delete, bulk, batch, merge, purge, wipe,
 # deprovision) before they reach your connected tools. Defense in depth on top of the
 # agent's confirm-before-write discipline. See write-guard.md.
 set -u

 input="$(cat)"

 # Pull the tool name without requiring jq (one well-formed field).
 tool_name="$(printf '%s' "$input" \
 | grep -o '"tool_name"[[:space:]]*:[[:space:]]*"[^"]*"' \
 | head -n1 \
 | sed 's/.*"tool_name"[[:space:]]*:[[:space:]]*"//; s/"$//')"

 # Govern MCP tool calls only; everything else passes through untouched.
 case "$tool_name" in
 mcp__*) : ;;
 *) exit 0 ;;
 esac

 # Allowlist: exact tool names you have reviewed and explicitly permit, space or
 # newline separated in ARTEMIS_WRITEGUARD_ALLOW. Example:
 # export ARTEMIS_WRITEGUARD_ALLOW="mcp__hubspot__archive_contact"
 for allowed in ${ARTEMIS_WRITEGUARD_ALLOW:-}; do
 [ "$tool_name" = "$allowed" ] && exit 0
 done

 # The destructive class: high-blast-radius operations no unattended agent should run.
 if printf '%s' "$tool_name" | grep -qiE 'delete|destroy|purge|wipe|bulk|batch|merge|deprovision'; then
 echo "Artemis write guard blocked a destructive operation: ${tool_name}. Delete, bulk, batch, and merge calls are not permitted through the guard. If this is genuinely needed, a person should run it directly, or add the exact tool name to ARTEMIS_WRITEGUARD_ALLOW after reviewing it." >&2
 exit 2
 fi

 exit 0
 ```

4. Verify it works. In Claude Code, ask the agent to attempt a delete on a connected tool
 (or dry-run one). You should see the tool call blocked with the guard's message. A normal
 single-field write or a read is untouched.

## Allowing a specific tool you have reviewed

If a destructively-named tool is one you have reviewed and want to permit, add its exact name
to the allowlist rather than removing the guard:

```bash
export ARTEMIS_WRITEGUARD_ALLOW="mcp__hubspot__archive_contact mcp__yourcrm__merge_reviewed"
```

Keep the allowlist short and specific. Widening it back to the whole destructive class defeats
the point.
