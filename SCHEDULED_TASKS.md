# Claude Code — Scheduled Tasks

Scheduled tasks let Claude re-run a prompt automatically on an interval, within a session. Use them to poll a deployment, babysit a PR, check on a long-running build, or set a one-time reminder.

Requires Claude Code v2.1.72 or later.

## Scheduling Options

| | `/loop` (in-session) | Desktop scheduled tasks | Cloud routines |
|--|--|--|--|
| Runs on | Your machine | Your machine | Anthropic cloud |
| Machine must be on | Yes | Yes | No |
| Session must be open | Yes | No | No |
| Local file access | Yes | Yes | No |
| Min interval | 1 minute | 1 minute | 1 hour |

Use `/loop` for quick polling during a session. Use Desktop tasks or Routines for persistent automation.

## /loop — Run a Prompt Repeatedly

### Fixed interval
```
/loop 5m check if the deployment finished
/loop 30m /review-pr 1234
```

Supported units: `s` (seconds), `m` (minutes), `h` (hours), `d` (days).

### Let Claude choose the interval
```
/loop check whether CI passed and address any review comments
```
Claude picks a delay dynamically (1 min – 1 hour) based on what it observes — shorter while a build is active, longer when quiet.

### Built-in maintenance prompt
```
/loop
```
With no prompt, Claude runs a built-in maintenance loop: continues unfinished work, tends to the current PR (review comments, failed CI, merge conflicts), and runs cleanup passes when nothing is pending.

### Stop a loop
Press `Esc` while the loop is waiting for the next iteration.

## One-Time Reminders

Just describe it in natural language:
```
remind me at 3pm to push the release branch
in 45 minutes, check whether the integration tests passed
```

## Custom Default Loop Prompt (loop.md)

Create `.claude/loop.md` (project-level) or `~/.claude/loop.md` (user-level) to replace the built-in maintenance prompt:

```markdown
# .claude/loop.md
Check the `release/next` PR. If CI is red, pull the failing job log,
diagnose, and push a minimal fix. If new review comments have arrived,
address each one and resolve the thread. If everything is green, say so.
```

Edits take effect on the next iteration.

## Managing Tasks

```
what scheduled tasks do I have?
cancel the deploy check job
```

Claude uses `CronCreate`, `CronList`, and `CronDelete` under the hood. Each session can hold up to 50 tasks.

## Cron Expression Reference

Standard 5-field format: `minute hour day-of-month month day-of-week`

| Expression | Meaning |
|-----------|---------|
| `*/5 * * * *` | Every 5 minutes |
| `0 * * * *` | Every hour |
| `0 9 * * *` | Every day at 9am local |
| `0 9 * * 1-5` | Weekdays at 9am |
| `30 14 15 3 *` | March 15 at 2:30pm |

All times use your local timezone, not UTC.

## Limits

- Tasks only fire while Claude Code is running and idle — closing the terminal stops them
- No catch-up for missed fires (fires once when Claude becomes idle again)
- Tasks auto-expire after 7 days (resume with `--resume` to restore unexpired tasks)
- Set `CLAUDE_CODE_DISABLE_CRON=1` to disable the scheduler entirely

## Resources

- [Scheduled tasks documentation](https://code.claude.com/docs/en/scheduled-tasks)
- [Desktop scheduled tasks](https://code.claude.com/docs/en/desktop-scheduled-tasks)
- [Cloud routines](https://code.claude.com/docs/en/routines)
