# Claude Code — Memory

Each Claude Code session starts with a fresh context window. Two mechanisms carry knowledge across sessions: **CLAUDE.md files** (instructions you write) and **auto memory** (notes Claude writes itself).

## CLAUDE.md Files

Plain markdown files Claude reads at the start of every session.

### When to add to CLAUDE.md

- Claude makes the same mistake a second time
- A code review catches something Claude should have known
- You type the same correction into chat that you typed last session
- A new teammate would need the same context to be productive

### File locations

| Scope | Location | Shared with |
|-------|----------|------------|
| Managed policy | `/etc/claude-code/CLAUDE.md` (Linux) | All users on the machine |
| User | `~/.claude/CLAUDE.md` | Just you (all projects) |
| Project | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Team (via git) |
| Local (private) | `./CLAUDE.local.md` | Just you (add to `.gitignore`) |

Files in parent directories are also loaded, ordered from root down to your working directory.

### Creating a CLAUDE.md

```bash
# Auto-generate one from your codebase
/init
```

Or create it manually and add instructions like:

```markdown
# My Project

## Build & Test
- Run `npm test` before committing
- Use `npm run build` to compile

## Conventions
- 2-space indentation
- API handlers live in `src/api/handlers/`
- Never push directly to main
```

### Tips for effective instructions

- **Be specific**: "Use 2-space indentation" beats "format code properly"
- **Keep it short**: target under 200 lines per file
- **No contradictions**: conflicting rules may be ignored arbitrarily
- **Use headers and bullets**: structure helps Claude scan

### Import other files

```markdown
See @README for project overview and @package.json for npm commands.

# Git workflow
@docs/git-instructions.md
```

### Path-scoped rules (`.claude/rules/`)

Rules in `.claude/rules/*.md` load at session start. Add YAML frontmatter to scope a rule to specific files — it only enters context when Claude works with matching files:

```markdown
---
paths:
  - "src/api/**/*.ts"
---

# API Rules
- All endpoints must include input validation
- Use the standard error response format
```

## Auto Memory

Claude accumulates knowledge automatically — build commands, debugging insights, code style preferences — without you writing anything.

Requires Claude Code v2.1.59 or later.

### Enable/disable

Auto memory is on by default. Toggle it with `/memory` or in settings:
```json
{ "autoMemoryEnabled": false }
```

### Storage

```
~/.claude/projects/<project>/memory/
├── MEMORY.md          # Index, loaded into every session (first 200 lines)
├── debugging.md       # Topic files loaded on demand
└── api-conventions.md
```

All worktrees and subdirectories within the same git repo share one auto memory directory.

### How it works

- `MEMORY.md` (first 200 lines / 25KB) loads into every session automatically
- Topic files are read on demand when Claude needs specific information
- Claude decides what's worth saving — it doesn't write something every session

### View and edit

```
/memory
```

Lists all loaded CLAUDE.md files, lets you toggle auto memory, and links to the memory folder. Everything is plain markdown you can edit or delete at any time.

## Troubleshooting

**Claude isn't following my CLAUDE.md:**
- Run `/memory` to verify the file is listed (if it's not there, Claude can't see it)
- Make instructions more specific
- Check for conflicting instructions across multiple CLAUDE.md files
- For rules that *must* run, use a [hook](./HOOKS.md) instead

**Instructions lost after `/compact`:**
- Project-root CLAUDE.md survives compaction and is re-injected automatically
- Nested CLAUDE.md files reload the next time Claude reads a file in that subdirectory
- Add conversation-only instructions to CLAUDE.md to make them persist

## Resources

- [Memory documentation](https://code.claude.com/docs/en/memory)
- [Skills](https://code.claude.com/docs/en/skills) — task-specific instructions that load on demand
- [Hooks](./HOOKS.md) — enforced shell commands at lifecycle events
