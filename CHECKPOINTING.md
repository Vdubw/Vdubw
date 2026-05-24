# Claude Code — Checkpointing

Checkpointing automatically tracks Claude's file edits as you work, so you can rewind to any prior state if something goes wrong. Every user prompt creates a checkpoint. Checkpoints persist across sessions and are cleaned up after 30 days.

## Open the Rewind Menu

Two ways to open it:

```
/rewind
```

Or press `Esc` twice when the prompt input is empty.

The menu lists every prompt you sent during the session. Select one, then choose an action.

## Actions

| Action | What it does |
|--------|-------------|
| **Restore code and conversation** | Reverts both files and chat history to that point |
| **Restore conversation** | Rewinds chat history; keeps current code as-is |
| **Restore code** | Reverts file changes; keeps the conversation |
| **Summarize from here** | Compresses the selected message and everything after into a summary |
| **Summarize up to here** | Compresses everything before the selected message into a summary |
| **Never mind** | Returns to the message list without changes |

After restoring the conversation or choosing "Summarize from here," the original prompt from the selected message is restored into the input field so you can re-send or edit it.

## Restore vs. Summarize

- **Restore** options revert state — files, conversation, or both
- **Summarize** options compress part of the conversation without changing files on disk, freeing context window space while keeping Claude working in the same session

Use **Summarize from here** to discard a side discussion while keeping early context intact.
Use **Summarize up to here** to compress early setup discussion while keeping recent work in full detail.

To branch off and try a different approach while keeping the original session intact, use `claude --continue --fork-session` instead.

## Common Use Cases

- **Exploring alternatives**: try a different implementation, then rewind if it doesn't work
- **Recovering from mistakes**: quickly undo changes that introduced bugs
- **Freeing context space**: summarize a verbose debugging session from the midpoint forward
- **Iterating on features**: experiment with variations, knowing you can revert

## Limitations

- **Bash command changes are not tracked** — only edits made through Claude's file editing tools (Edit, Write, etc.) can be rewound. `rm`, `mv`, `cp` via Bash are not captured.
- **External changes not tracked** — files modified outside Claude Code in the current session aren't captured (unless Claude also touched them)
- **Not a replacement for git** — think of checkpoints as "local undo" and git as "permanent history"

## Resources

- [Checkpointing documentation](https://code.claude.com/docs/en/checkpointing)
- [Interactive mode](https://code.claude.com/docs/en/interactive-mode) — keyboard shortcuts and session controls
- [Sessions](https://code.claude.com/docs/en/sessions) — forking, naming, and resuming sessions
