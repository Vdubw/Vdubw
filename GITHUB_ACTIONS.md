# Claude Code — GitHub Actions

Bring AI-powered automation into your GitHub workflow. Mention `@claude` in any PR or issue comment and Claude can analyze code, implement features, fix bugs, and create pull requests — all following your project's standards defined in `CLAUDE.md`.

## Quick Setup

The easiest way is from inside Claude Code:

```
/install-github-app
```

This guides you through installing the GitHub app and adding the required secrets. You must be a repo admin.

**Or set it up manually:**
1. Install the Claude GitHub app: https://github.com/apps/claude
2. Add `ANTHROPIC_API_KEY` to your repository secrets
3. Copy [the example workflow](https://github.com/anthropics/claude-code-action/blob/main/examples/claude.yml) to `.github/workflows/`

## Basic Workflow

```yaml
name: Claude Code
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

After setup, trigger Claude by mentioning `@claude` in any issue or PR comment:

```
@claude implement this feature based on the issue description
@claude fix the TypeError in the user dashboard component
@claude how should I approach adding rate limiting to this endpoint?
```

## Key Parameters

| Parameter | Description | Required |
|-----------|-------------|---------|
| `prompt` | Instructions for Claude (or a skill name like `/code-review`) | No |
| `claude_args` | CLI flags passed directly to Claude Code | No |
| `anthropic_api_key` | Your API key | Yes (for direct API) |
| `github_token` | GitHub token for API access | No |
| `trigger_phrase` | Custom trigger (default: `@claude`) | No |
| `use_bedrock` | Use Amazon Bedrock instead | No |
| `use_vertex` | Use Google Vertex AI instead | No |

### Common `claude_args`

```yaml
claude_args: "--max-turns 10 --model claude-sonnet-4-6"
```

## Automated Workflows (no trigger needed)

```yaml
# Daily commit summary
name: Daily Report
on:
  schedule:
    - cron: "0 9 * * *"
jobs:
  report:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Summarize yesterday's commits and open issues"
```

```yaml
# Auto code review on every PR
name: Code Review
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          plugin_marketplaces: "https://github.com/anthropics/claude-code.git"
          plugins: "code-review@claude-code-plugins"
          prompt: "/code-review:code-review ${{ github.repository }}/pull/${{ github.event.pull_request.number }}"
```

## Best Practices

- **Use `CLAUDE.md`** to define coding standards, review criteria, and project conventions — Claude follows these automatically
- **Never hardcode API keys** — always use `${{ secrets.ANTHROPIC_API_KEY }}`
- **Set `--max-turns`** to limit API costs on complex tasks
- **Review Claude's PRs** before merging

## Cost Awareness

- GitHub Actions minutes are consumed for each run
- API tokens are consumed based on prompt/response length
- Use `--max-turns` in `claude_args` to cap iterations
- Consider concurrency limits to avoid parallel runaway jobs

## Upgrading from Beta

If you used `@beta`, update to `@v1`:

| Old | New |
|-----|-----|
| `mode: "tag"` | *(removed — auto-detected)* |
| `direct_prompt` | `prompt` |
| `custom_instructions` | `claude_args: --append-system-prompt` |
| `max_turns` | `claude_args: --max-turns` |
| `model` | `claude_args: --model` |

## Resources

- [GitHub Actions documentation](https://code.claude.com/docs/en/github-actions)
- [Claude Code Action repo](https://github.com/anthropics/claude-code-action)
- [Security documentation](https://github.com/anthropics/claude-code-action/blob/main/docs/security.md)
- [Example workflows](https://github.com/anthropics/claude-code-action/tree/main/examples)
