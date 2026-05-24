# Claude Code — Computer Use

Computer use lets Claude open apps, click, type, scroll, and see your screen — from the CLI. Claude can build a native app, launch it, click through every control, and screenshot the result, all in the same conversation where it wrote the code.

**macOS only. Requires Pro or Max plan. Claude Code v2.1.85+.**

## What You Can Do

- **Validate native builds**: write Swift, compile, launch, and click through the UI to verify before you open it
- **End-to-end UI testing**: "test the onboarding flow" — Claude opens the app, clicks through signup, screenshots each step
- **Debug visual issues**: "the modal clips on small windows" — Claude resizes, reproduces the bug, patches CSS, verifies the fix
- **Drive GUI-only tools**: anything without a CLI or API — design tools, hardware panels, iOS Simulator

## Enable Computer Use

```
/mcp
```

Find `computer-use` in the server list and select **Enable**. The setting persists per project.

On first use, macOS will prompt for two permissions:
- **Accessibility** — lets Claude click, type, scroll
- **Screen Recording** — lets Claude see your screen

Grant both. You may need to restart Claude Code after granting Screen Recording.

## How It Works

1. **One session at a time** — computer use holds a machine-wide lock. If another session is active, new attempts fail.
2. **Apps are hidden while Claude works** — other visible apps hide so Claude only interacts with approved ones. Your terminal stays visible and is excluded from screenshots.
3. **Screenshots are auto-downscaled** — Retina displays are downscaled automatically; no need to change your resolution.
4. **Stop at any time** — press `Esc` anywhere to abort immediately. A macOS notification appears when Claude starts and when it finishes.

## Per-Session App Approval

Enabling the server doesn't grant Claude access to every app. The first time Claude needs a specific app, a prompt appears in your terminal:
- Which apps Claude wants to control
- Any extra permissions (e.g. clipboard)
- How many apps will be hidden

Choose **Allow for this session** or **Deny**. Apps with broad reach show extra warnings:

| Warning | Apps |
|---------|------|
| Equivalent to shell access | Terminal, VS Code, iTerm, Warp |
| Can read/write any file | Finder |
| Can change system settings | System Settings |

## Example Workflows

```
# Validate a native build
Build the MenuBarStats target, launch it, open preferences,
and verify the interval slider updates the label. Screenshot when done.

# Reproduce a layout bug
The settings modal clips its footer on narrow windows.
Resize until you reproduce it, screenshot, then check the CSS.

# Test an iOS Simulator flow
Open the iOS Simulator, launch the app, tap through onboarding,
and tell me if any screen takes more than a second to load.
```

## When Claude Uses Computer Use

Claude picks the most precise tool first:
1. MCP server (if one exists for the service)
2. Bash (for shell commands)
3. Claude in Chrome (for browser tasks)
4. Computer use (for native apps, simulators, GUI-only tools)

## Safety

- Per-app approval required each session
- Sentinel warnings for high-risk apps (terminal, Finder, System Settings)
- Terminal excluded from screenshots — Claude never sees its own output
- `Esc` key is consumed by the global escape, so prompt injection can't dismiss dialogs
- Lock file prevents two sessions from controlling the machine simultaneously

## Troubleshooting

**"Computer use is in use by another Claude session"** — Exit the other session first. If it crashed, the lock releases automatically.

**Permission prompt keeps reappearing** — Quit Claude Code completely and restart. Check System Settings → Privacy & Security → Screen Recording.

**`computer-use` not in `/mcp`** — Check:
- macOS only (not Linux/Windows for CLI)
- Claude Code v2.1.85+
- Pro or Max plan (`/status` to verify)
- claude.ai authentication (not Bedrock/Vertex/Foundry)
- Interactive session (not `-p` flag)

## Resources

- [Computer use documentation](https://code.claude.com/docs/en/computer-use)
- [Computer use safety guide](https://support.claude.com/en/articles/14128542)
- [Claude in Chrome](https://code.claude.com/docs/en/chrome) — browser automation
- [MCP](./MCP.md) — structured tool connections
