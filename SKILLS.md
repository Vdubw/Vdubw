# Claude Code — Skills

Skills extend what Claude can do. Create a `SKILL.md` file with instructions and Claude adds it to its toolkit — loading it automatically when relevant, or letting you invoke it directly with `/skill-name`.

Use a skill when you keep pasting the same instructions into chat, or when a section of CLAUDE.md has grown into a multi-step procedure. Unlike CLAUDE.md content, a skill's body loads only when used.

## Built-in Skills

Claude Code ships with bundled skills available in every session:

| Skill | Purpose |
|-------|---------|
| `/code-review` | Review code for correctness and style |
| `/debug` | Systematic debugging workflow |
| `/loop` | Run a prompt repeatedly on a schedule |
| `/batch` | Split a large change into parallel worktree-isolated subagents |
| `/run` | Launch your app and verify a change works |
| `/verify` | Confirm a change does what it should (without just running tests) |

## Create a Skill

Each skill is a directory with a `SKILL.md` file:

```
~/.claude/skills/my-skill/
└── SKILL.md
```

**Basic example:**
```markdown
---
description: Summarizes uncommitted changes and flags anything risky. Use when asked what changed or to review a diff.
---

## Current changes

!`git diff HEAD`

## Instructions

Summarize the changes in 2-3 bullets, then list any risks (missing error handling, hardcoded values, tests that need updating).
```

The `` !`git diff HEAD` `` line runs the command before Claude sees the skill — the output is inlined automatically.

## Skill Locations

| Location | Path | Scope |
|----------|------|-------|
| Personal | `~/.claude/skills/<name>/SKILL.md` | All your projects |
| Project | `.claude/skills/<name>/SKILL.md` | This project only |
| Plugin | `<plugin>/skills/<name>/SKILL.md` | Where plugin is enabled |

## Frontmatter Options

```yaml
---
name: my-skill               # Display name (defaults to directory name)
description: What it does    # Helps Claude decide when to load it
argument-hint: [issue-num]   # Shown during autocomplete
disable-model-invocation: true  # Only YOU can invoke (not Claude automatically)
user-invocable: false        # Only CLAUDE can invoke (hidden from / menu)
allowed-tools: Bash(git *)   # Tools auto-approved when skill is active
context: fork                # Run in an isolated subagent
agent: Explore               # Which subagent type to use with context: fork
---
```

## Invoke a Skill

```
/my-skill                    # Direct invocation
/my-skill some arguments     # With arguments ($ARGUMENTS in skill content)
```

Claude also loads skills automatically based on their `description` when your prompt matches.

## Pass Arguments

```markdown
---
name: fix-issue
disable-model-invocation: true
---

Fix GitHub issue $ARGUMENTS following our coding standards.
1. Read the issue
2. Implement the fix
3. Write tests
```

Run `/fix-issue 123` → Claude sees "Fix GitHub issue 123..."

Access specific args: `$0`, `$1`, or `$ARGUMENTS[0]`, `$ARGUMENTS[1]`.

## Inject Dynamic Context

Use `` !`command` `` to run shell commands and inline their output before Claude sees the skill:

```markdown
## Current branch status
!`git status --short`

## Recent commits
!`git log --oneline -5`
```

For multi-line commands, use a fenced code block with ` ```! `:

````markdown
```!
node --version
npm --version
```
````

## Run in a Subagent

Add `context: fork` to isolate the skill in its own context (no conversation history):

```yaml
---
name: research
context: fork
agent: Explore
---

Research $ARGUMENTS:
1. Find relevant files with Glob and Grep
2. Summarize findings with file references
```

## Supporting Files

Skills can include additional files in their directory:

```
my-skill/
├── SKILL.md         # Main instructions (required)
├── reference.md     # Detailed docs — reference from SKILL.md
├── examples.md      # Examples
└── scripts/
    └── helper.py    # Scripts Claude can run
```

Keep `SKILL.md` under 500 lines. Move detail into separate files and reference them.

## Control Claude's Access to Skills

```
# In /permissions, deny the Skill tool to block all skills:
Skill

# Allow only specific skills:
Skill(commit)
Skill(review-pr *)

# Deny specific skills:
Skill(deploy *)
```

## Tips

- Skills load descriptions into context so Claude knows what's available; full content loads only when invoked
- After `/compact`, Claude re-attaches recently invoked skills automatically
- Add `disable-model-invocation: true` for skills with side effects (deploy, commit, send-message)
- Run `/doctor` to check if your skill descriptions are being truncated due to budget overflow

## Resources

- [Skills documentation](https://code.claude.com/docs/en/skills)
- [Commands reference](https://code.claude.com/docs/en/commands) — built-in commands and bundled skills
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Plugins](https://code.claude.com/docs/en/plugins) — package and distribute skills
