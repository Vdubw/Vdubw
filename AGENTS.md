# Claude Code — Agents & Parallel Work

Claude Code offers several ways to run multiple tasks at once. The right approach depends on who coordinates the work and whether tasks touch the same files.

## Comparison

| Approach | What it gives you | Use it when |
|----------|------------------|-------------|
| **Subagents** | Delegated workers inside one session; return a summary | A side task would flood your main conversation with results you won't reference again |
| **Agent view** (`claude agents`) | One screen to dispatch and monitor background sessions | You have independent tasks to hand off and check back on |
| **Agent teams** | Coordinated sessions with a shared task list and messaging | You want Claude to split a project into pieces and keep workers in sync (experimental) |
| **Worktrees** | Separate git checkouts so parallel sessions never conflict | Running several sessions that edit overlapping files |
| **`/batch`** | Splits one large change into 5–30 isolated subagents, each opening a PR | Repo-wide migrations or mechanical refactors |

## Subagents

Ask Claude to "use a subagent for this" or define custom ones in `.claude/agents/`. Subagents do a focused task in their own context window and report back a summary, keeping your main conversation clean.

Check on running subagents with `/agents` → **Running** tab.

## Agent View

```bash
claude agents
```

Opens a screen showing every background session, its state, and which ones need your input. Dispatched sessions automatically get their own worktrees when they need to edit files.

## Agent Teams (Experimental)

A lead agent plans the work, assigns tasks to teammate agents, and coordinates via a shared task list and inter-agent messages. Disabled by default — enable in settings before use.

Key tip: partition work so each teammate owns a different set of files to avoid conflicts.

## Worktrees

See [WORKTREES.md](./WORKTREES.md) for the full guide.

## Checking on Work

| You used | Check with |
|----------|-----------|
| Background sessions | `claude agents` |
| Subagents in current session | `/agents` → Running tab |
| Anything in the background | `/tasks` |

## Cost Note

Running several sessions or subagents at once multiplies token usage. See the [Costs docs](https://code.claude.com/docs/en/costs) for rate-limit details.

## Resources

- [Run agents in parallel](https://code.claude.com/docs/en/agents)
- [Create custom subagents](https://code.claude.com/docs/en/sub-agents)
- [Manage agents with agent view](https://code.claude.com/docs/en/agent-view)
- [Orchestrate agent teams](https://code.claude.com/docs/en/agent-teams)
