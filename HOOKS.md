# Claude Code — Hooks

Hooks are shell commands that run automatically at specific points in Claude Code's lifecycle. They give you deterministic control — certain actions always happen regardless of what Claude decides to do.

Common uses: auto-format code, block edits to sensitive files, send notifications, re-inject context after compaction, enforce project rules.

## Hook Events

| Event | When it fires |
|-------|--------------|
| `PreToolUse` | Before Claude calls a tool (can block it) |
| `PostToolUse` | After a tool completes |
| `Notification` | When Claude needs your input or permission |
| `SessionStart` | At session start (or after compaction with `compact` matcher) |
| `Stop` | When Claude finishes a turn |

## Configuration

Add a `hooks` block to `~/.claude/settings.json` (user-wide) or `.claude/settings.json` (project):

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolName|OtherTool",
        "hooks": [
          {
            "type": "command",
            "command": "your-shell-command"
          }
        ]
      }
    ]
  }
}
```

View configured hooks with `/hooks` inside Claude Code.

## Common Examples

### Get notified when Claude needs input

**macOS:**
```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude needs your attention\" with title \"Claude Code\"'"
          }
        ]
      }
    ]
  }
}
```

**Linux:**
```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "",
        "hooks": [{ "type": "command", "command": "notify-send 'Claude Code' 'Needs your attention'" }]
      }
    ]
  }
}
```

### Auto-format code after edits

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | xargs npx prettier --write"
          }
        ]
      }
    ]
  }
}
```

### Block edits to protected files

Create `.claude/hooks/protect-files.sh`:
```bash
#!/bin/bash
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')
PROTECTED_PATTERNS=(".env" "package-lock.json" ".git/")
for pattern in "${PROTECTED_PATTERNS[@]}"; do
  if [[ "$FILE_PATH" == *"$pattern"* ]]; then
    echo "Blocked: $FILE_PATH matches protected pattern '$pattern'" >&2
    exit 2
  fi
done
exit 0
```

Register it:
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [{ "type": "command", "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/protect-files.sh" }]
      }
    ]
  }
}
```

### Re-inject context after compaction

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "compact",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Reminder: use Bun, not npm. Run bun test before committing.'"
          }
        ]
      }
    ]
  }
}
```

## Exit Codes (PreToolUse)

| Code | Effect |
|------|--------|
| `0` | Allow the tool call to proceed |
| `2` | Block the tool call; Claude sees your stderr as feedback |
| other | Non-blocking error (logged, tool still runs) |

## Hook Location

Hooks defined in `~/.claude/settings.json` apply across all projects. Hooks in `.claude/settings.json` apply only to the current project.

## Resources

- [Hooks guide](https://code.claude.com/docs/en/hooks-guide)
- [Hooks reference](https://code.claude.com/docs/en/hooks) — full event schemas and advanced features
