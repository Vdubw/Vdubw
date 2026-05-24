# Claude Code Remote Control

Remote Control lets you continue a local Claude Code session from your phone, tablet, or any browser via [claude.ai/code](https://claude.ai/code) or the Claude mobile app (iOS / Android).

Your session always runs on your local machine — Remote Control is just a window into it.

## Requirements

- Claude Code v2.1.51 or later (`claude --version`)
- A Pro, Max, Team, or Enterprise subscription (API keys are not supported)
- Signed in with `claude` → `/login` via claude.ai
- Workspace trust accepted: run `claude` in your project directory at least once

## Starting a Session

**Server mode** — stays running and accepts multiple remote connections:
```bash
claude remote-control
claude remote-control --name "My Project"
```

**Interactive mode** — full local session that is also remotely accessible:
```bash
claude --remote-control
claude --remote-control "My Project"
```

**From an existing session** — run inside Claude Code to share the current conversation:
```
/remote-control
/remote-control My Project
```

**VS Code** — type `/remote-control` or `/rc` in the prompt box (requires v2.1.79+).

Once active, a session URL and QR code are displayed. Open the URL in any browser or scan the QR code with the Claude mobile app to connect.

## Connecting from Another Device

- Open the **session URL** directly in any browser
- **Scan the QR code** (press `spacebar` in server mode to toggle display)
- Open **claude.ai/code** or the **Claude app** and find the session by name in the list

## Server Mode Flags

| Flag | Description |
|------|-------------|
| `--name "Title"` | Custom session title |
| `--spawn same-dir` | All sessions share the current directory (default) |
| `--spawn worktree` | Each session gets its own git worktree |
| `--spawn session` | Single-session mode; rejects additional connections |
| `--capacity <N>` | Max concurrent sessions (default: 32) |
| `--sandbox` | Enable filesystem/network sandboxing |
| `--verbose` | Show detailed connection logs |

## Security

- Claude Code makes **outbound HTTPS only** — no inbound ports are opened
- All traffic flows through the Anthropic API over TLS
- Short-lived, purpose-scoped credentials are used for each connection

## Push Notifications

Claude can notify your phone when a long task finishes or when it needs input. Requires Claude Code v2.1.110+.

Setup:
1. Install the Claude mobile app and sign in with the same account
2. Allow notifications from the OS prompt
3. Run `/config` in Claude Code and enable **Push when Claude decides**

## Limitations

- The local `claude` process must keep running; closing the terminal ends the session
- After ~10 minutes without network access, the session times out
- Starting an Ultraplan session disconnects an active Remote Control session
- Commands that open interactive terminal pickers (`/mcp`, `/plugin`, `/resume`) are local-only

## Remote Control vs Claude Code on the Web

| | Remote Control | Claude Code on the Web |
|---|---|---|
| Runs on | Your machine | Anthropic cloud |
| Local filesystem | Yes | No |
| Local MCP servers | Yes | No |
| Setup required | `claude remote-control` | None |
| Best for | Continuing in-progress local work | Starting fresh tasks without local setup |

## Resources

- [Full documentation](https://code.claude.com/docs/en/remote-control)
- [Authentication setup](https://code.claude.com/docs/en/authentication)
- [CLI reference](https://code.claude.com/docs/en/cli-reference)
- [Claude Code on the Web](https://code.claude.com/docs/en/claude-code-on-the-web)
