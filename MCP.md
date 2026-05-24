# Claude Code — MCP (Model Context Protocol)

MCP connects Claude Code to external tools, databases, and APIs. Instead of copying data into chat from another tool, you connect a server and Claude reads and acts on that system directly.

## What You Can Do

- "Add the feature described in JIRA issue ENG-4521 and create a PR"
- "Check Sentry for errors introduced in the last deploy"
- "Query our PostgreSQL database for revenue this month"
- "Create Gmail drafts inviting users to a feedback session"
- React to external events (CI failures, Telegram messages, webhooks) via Channels

Browse reviewed connectors at the [Anthropic Directory](https://claude.ai/directory).

## Adding Servers

### Remote HTTP server (recommended)
```bash
claude mcp add --transport http <name> <url>

# Examples
claude mcp add --transport http notion https://mcp.notion.com/mcp
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_GITHUB_PAT"
```

### Local stdio server
```bash
claude mcp add --transport stdio --env AIRTABLE_API_KEY=YOUR_KEY airtable \
  -- npx -y airtable-mcp-server
```

### Manage servers
```bash
claude mcp list
claude mcp get <name>
claude mcp remove <name>
# Inside Claude Code:
/mcp
```

## Scopes

| Scope | Stored in | Shared with team |
|-------|-----------|-----------------|
| `local` (default) | `~/.claude.json` | No |
| `project` | `.mcp.json` in project root | Yes (via git) |
| `user` | `~/.claude.json` | No (all projects) |

```bash
claude mcp add --scope project --transport http sentry https://mcp.sentry.dev/mcp
```

## Authentication

Many servers use OAuth 2.0. After adding the server, run `/mcp` to authenticate in your browser. Tokens are stored securely and refreshed automatically.

## Tool Search

By default, MCP tools are deferred — only names load at session start. Claude searches for relevant tools when it needs them, keeping context usage low. You can always access them; there's no extra step required.

To always load a server's tools upfront, set `alwaysLoad` in `.mcp.json`:
```json
{
  "mcpServers": {
    "core-tools": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "alwaysLoad": true
    }
  }
}
```

## Using Claude Code as an MCP Server

```bash
claude mcp serve
```

Add to Claude Desktop's `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "claude-code": {
      "type": "stdio",
      "command": "claude",
      "args": ["mcp", "serve"]
    }
  }
}
```

## Tips

- Use `@server:protocol://resource/path` mentions in prompts to reference MCP resources
- Set `MCP_TIMEOUT=10000 claude` to adjust server startup timeout
- Set `MAX_MCP_OUTPUT_TOKENS=50000` for servers that return large responses
- Import servers from Claude Desktop: `claude mcp add-from-claude-desktop`

## Resources

- [Full MCP documentation](https://code.claude.com/docs/en/mcp)
- [MCP server guide](https://modelcontextprotocol.io/docs/develop/build-server)
- [Anthropic Directory](https://claude.ai/directory)
