# Claude Code — CLI Installation

Claude Code is a command-line tool you install once and use everywhere. It requires Node.js 18+ and an Anthropic account.

## Install

```bash
npm install -g @anthropic-ai/claude-code
```

Verify:

```bash
claude --version
```

## Prerequisites

| Requirement | Minimum |
|-------------|---------|
| Node.js | 18 |
| npm | 8 |
| OS | macOS, Linux, Windows (WSL2) |

Check your Node version with `node --version`. If you need to upgrade, use [nvm](https://github.com/nvm-sh/nvm) or the [official installer](https://nodejs.org).

## First Run & Authentication

```bash
claude
```

On first launch Claude Code opens a browser window to authenticate with your Anthropic account. Once complete, credentials are stored at `~/.claude/` and you won't be prompted again.

## Update

```bash
npm update -g @anthropic-ai/claude-code
```

Or let Claude Code update itself — it will prompt you when a new version is available at session start.

## Uninstall

```bash
npm uninstall -g @anthropic-ai/claude-code
```

To also remove stored credentials and settings:

```bash
rm -rf ~/.claude
```

## IDE Extensions

Claude Code integrates directly into VS Code and JetBrains IDEs. The extension surfaces the same agent in a sidebar panel and shares the same auth — no separate login.

- **VS Code**: search *Claude Code* in the Extensions panel
- **JetBrains**: search *Claude Code* in Settings → Plugins

## Platform Notes

### macOS / Linux

Works out of the box after the npm install. On macOS, if you see a permissions error, avoid `sudo npm install -g` — instead fix npm's global prefix:

```bash
mkdir -p ~/.npm-global
npm config set prefix ~/.npm-global
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc  # or ~/.bashrc
source ~/.zshrc
npm install -g @anthropic-ai/claude-code
```

### Windows

Native Windows is not supported. Use [WSL2](https://learn.microsoft.com/en-us/windows/wsl/install) and run the install inside your WSL2 terminal.

## Key Directories

| Path | Purpose |
|------|---------|
| `~/.claude/settings.json` | User-wide settings (hooks, permissions, theme) |
| `~/.claude/` | Auth tokens, memory, keybindings |
| `.claude/settings.json` | Project-level settings (checked into git) |
| `CLAUDE.md` | Project instructions Claude reads automatically |

## Resources

- [Quickstart](https://code.claude.com/docs/en/getting-started)
- [Claude Code on the web](https://code.claude.com/docs/en/claude-code-on-the-web) — run sessions in the cloud without a local install
- [IDE integrations](https://code.claude.com/docs/en/ide-integrations)
