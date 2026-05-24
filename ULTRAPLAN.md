# Claude Code — Ultraplan

Ultraplan hands a planning task from your local CLI to a cloud session running in plan mode. Claude drafts the plan remotely while your terminal stays free. You review it in your browser — commenting on specific sections, requesting revisions — then choose to execute it in the cloud or send it back to your terminal.

Requires Claude Code v2.1.91+, a claude.ai account, and a GitHub repository.

## When to Use Ultraplan

- You want **targeted feedback** on individual sections of a plan (inline comments, emoji reactions)
- You want **hands-off drafting** — Claude plans remotely while you keep working
- You want to **choose where to run** the plan: cloud (opens a PR) or local terminal (full environment access)

## Launch Ultraplan

Three ways to start:

```bash
# Slash command
/ultraplan migrate the auth service from sessions to JWTs

# Keyword in a normal prompt
ultraplan — refactor the payment module to use the new SDK

# From a finished local plan
# When Claude shows the approval dialog, choose:
# "No, refine with Ultraplan on Claude Code on the web"
```

A confirmation dialog appears (except for the local plan path). After launching, your CLI shows a status indicator:

| Status | Meaning |
|--------|---------|
| `◇ ultraplan` | Claude is researching and drafting |
| `◇ ultraplan needs your input` | Claude has a question — open the session link |
| `◆ ultraplan ready` | Plan is ready to review |

Run `/tasks` and select the ultraplan entry to view details or stop it.

## Review the Plan

When the status shows `◆ ultraplan ready`, open the session link. In the browser you can:

- **Comment on specific sections** — highlight any passage and leave feedback
- **React with emoji** — signal approval or concern without writing
- **Jump between sections** via the outline sidebar
- **Request revisions** — Claude updates the plan and presents a new draft

Iterate as many times as needed.

## Execute the Plan

Once the plan looks right, choose from the browser:

### Run on the web
Click **Approve Claude's plan and start coding** — Claude implements it in the same cloud session. When done, review the diff and open a PR from the web interface.

### Send back to your terminal
Click **Approve plan and teleport back to terminal** — the web session archives and your terminal shows a dialog with three options:

| Option | What happens |
|--------|-------------|
| **Implement here** | Injects the plan into your current conversation |
| **Start new session** | Clears context and begins fresh with only the plan |
| **Cancel** | Saves the plan to a file; Claude prints the path |

## Notes

- Ultraplan disconnects any active [Remote Control](./REMOTE_CONTROL.md) session (both use the claude.ai/code interface)
- Not available on Amazon Bedrock, Google Vertex AI, or Microsoft Foundry
- The cloud session runs in your account's default cloud environment

## Resources

- [Ultraplan documentation](https://code.claude.com/docs/en/ultraplan)
- [Plan mode](https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode)
- [Ultrareview](https://code.claude.com/docs/en/ultrareview) — the code review counterpart
