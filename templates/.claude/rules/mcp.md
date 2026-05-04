---
description: When and how to wire MCP servers into the harness — prefer read-only, mind the token cost
paths:
  - ".mcp.json"
  - ".claude/settings.json"
---

# MCP Servers

MCP servers extend the agent's reach into external systems (issue trackers, databases, browsers, docs). They are powerful and expensive. Wire them deliberately.

## When to add an MCP

- The agent needs **fresh, authoritative data** that lives outside the repo (open tickets, current schema, recent dashboards).
- The data **changes faster** than a rule or feature doc could keep up.
- A read-only view is enough — write access multiplies the blast radius.

If a static rule, a feature doc, or a one-time `gh`/`jira` invocation would do the job, skip the MCP.

## Defaults

- **Read-only first.** Add write scopes only when a workflow demonstrably needs them.
- **Scope narrowly.** Production database MCPs read schema, not rows. Issue trackers read assigned tickets, not the whole org.
- **Token cost is real.** Browser-automation and large-search MCPs can dominate a session's budget. Reach for them only when their output is the point.

## Configuration

- MCP server configs live in `.claude/settings.json` or `.mcp.json`.
- Secrets come from the user's environment — never from the config file.

### Example: Puppeteer (browser automation)

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-server-puppeteer"]
    }
  }
}
```

Puppeteer is a token-expensive MCP — it returns rendered pages and screenshots. Reach for it only when verifying UI behavior is the point of the task; otherwise prefer a unit or integration test.

## Anti-patterns

- Wiring an MCP because it's available, not because a workflow needs it.
- Granting write scopes "to be safe" — that is the opposite of safe.
- Treating MCP output as ground truth without verification when the action is irreversible.
