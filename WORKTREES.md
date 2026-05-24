# Claude Code — Worktrees

A git worktree is a separate working directory with its own files and branch, sharing the same repository history. Running each Claude Code session in its own worktree means edits in one session never touch files in another — perfect for working on a feature and a bug fix simultaneously.

## Start Claude in a Worktree

```bash
# Named worktree — creates .claude/worktrees/feature-auth/ on branch worktree-feature-auth
claude --worktree feature-auth

# Auto-generated name (e.g. bright-running-fox)
claude --worktree

# Start a second session in parallel
claude --worktree bugfix-123
```

Run this in separate terminals for truly parallel, isolated work.

## Branch from a Pull Request

```bash
claude --worktree "#1234"
```

Claude fetches `pull/1234/head` from origin and creates the worktree at `.claude/worktrees/pr-1234`.

## Copy Local Config Files into Worktrees

Worktrees are fresh checkouts — `.env` and other gitignored files aren't present. Add a `.worktreeinclude` file to copy them automatically:

```
# .worktreeinclude
.env
.env.local
config/secrets.json
```

Only files that match a pattern AND are gitignored are copied (tracked files are never duplicated).

## Isolate Subagents with Worktrees

Ask Claude to "use worktrees for your agents," or set it permanently on a custom subagent:

```markdown
---
isolation: worktree
---
```

Each subagent gets a temporary worktree that is removed automatically when it finishes without changes.

## Base Branch

By default, new worktrees branch from `origin/HEAD` (a clean remote state). To branch from your current local `HEAD` instead (carrying unpushed commits), set in `.claude/settings.json`:

```json
{
  "worktree": {
    "baseRef": "head"
  }
}
```

## Cleanup

- **No changes made**: worktree and branch are removed automatically on exit
- **Changes exist**: Claude prompts you to keep or remove the worktree
- **Non-interactive (`-p`)**: not cleaned up automatically; remove with `git worktree remove`

## Manual Worktree Management

```bash
# Create a worktree on a new branch
git worktree add ../project-feature-a -b feature-a

# Create from an existing branch
git worktree add ../project-bugfix bugfix-123

# Start Claude in it
cd ../project-feature-a && claude

# List all worktrees
git worktree list

# Remove when done
git worktree remove ../project-feature-a
```

## Tips

- Add `.claude/worktrees/` to `.gitignore` so worktree contents don't appear as untracked files
- Remember to run your project's setup (install deps, activate virtualenv) in each new worktree
- The desktop app creates a worktree for every new session automatically

## Resources

- [Worktrees documentation](https://code.claude.com/docs/en/worktrees)
- [Run agents in parallel](https://code.claude.com/docs/en/agents)
- [Git worktree docs](https://git-scm.com/docs/git-worktree)
