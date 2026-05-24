# Claude Code — Channels

Channels let external events push messages into your running Claude Code session. Instead of polling, Claude reacts: CI fails, a Telegram message arrives, a webhook fires — Claude sees it immediately and can respond while you're away.

Requires Claude Code v2.1.80+, claude.ai or Console API key authentication.

## Supported Channels

| Channel | Platform | Notes |
|---------|----------|-------|
| Telegram | Mobile/desktop | Bot-based; you control who can message it |
| Discord | Desktop/browser | Bot-based; DM your bot to pair |
| iMessage | macOS only | Reads Messages database; no bot token needed |
| fakechat | localhost | Demo UI at http://localhost:8787; good for testing |

All channels are installed as plugins and require [Bun](https://bun.sh).

## Quick Start (fakechat demo)

```bash
# In Claude Code:
/plugin install fakechat@claude-plugins-official

# Restart with channel enabled:
claude --channels plugin:fakechat@claude-plugins-official
```

Open http://localhost:8787 and type a message. It arrives in your session; Claude replies and the answer shows up in the browser.

## Telegram Setup

```bash
# 1. Create a bot via @BotFather in Telegram — copy the token
# 2. In Claude Code:
/plugin install telegram@claude-plugins-official
/reload-plugins
/telegram:configure <token>

# 3. Restart:
claude --channels plugin:telegram@claude-plugins-official

# 4. Message your bot — it replies with a pairing code. Then:
/telegram:access pair <code>
/telegram:access policy allowlist
```

## Discord Setup

```bash
# 1. Create a bot at discord.com/developers — enable Message Content Intent
# 2. Invite bot to your server (needs: View Channels, Send Messages, Read History)
# 3. In Claude Code:
/plugin install discord@claude-plugins-official
/reload-plugins
/discord:configure <token>

# 4. Restart:
claude --channels plugin:discord@claude-plugins-official

# 5. DM your bot — it replies with a pairing code. Then:
/discord:access pair <code>
/discord:access policy allowlist
```

## iMessage Setup (macOS)

```bash
/plugin install imessage@claude-plugins-official
claude --channels plugin:imessage@claude-plugins-official

# Text yourself to test (self-chat bypasses access control)
# Allow other contacts:
/imessage:access allow +15551234567
```

Grant Full Disk Access to your terminal in System Settings → Privacy & Security when prompted.

## Security

Every channel maintains a **sender allowlist** — only IDs you've paired can push messages. The pairing flow:
1. Send any message to your bot
2. Bot replies with a pairing code
3. Run `/telegram:access pair <code>` in Claude Code
4. Lock it down: `/telegram:access policy allowlist`

## How Channels Compare

| Feature | Channels | Remote Control | Claude on Web |
|---------|----------|---------------|---------------|
| Trigger | External event push | You drive the session | You start a task |
| Runs on | Your machine | Your machine | Anthropic cloud |
| Good for | Reacting while away | Steering in progress | Fresh async tasks |

## Tips

- Multiple channels can run at once: `claude --channels plugin:telegram@... plugin:discord@...`
- If Claude hits a permission prompt while you're away, the session pauses until you respond
- Claude replies appear on the external platform, not in the terminal

## Resources

- [Channels documentation](https://code.claude.com/docs/en/channels)
- [Build your own channel](https://code.claude.com/docs/en/channels-reference)
- [Official channel plugins](https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins)
