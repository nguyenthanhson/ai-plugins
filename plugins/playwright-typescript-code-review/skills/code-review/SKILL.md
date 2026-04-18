---
name: code-review
description: Review a Playwright TypeScript pull request for reliability, maintainability, and correctness. Discovers repository standards first, then reviews the diff. Requires explicit base and head SHAs. Usage — /code-review base=<sha> head=<sha> or /code-review base=<sha> head=<sha> --output <path>
---

# Code Review (PR Mode)

Invoke the `playwright-typescript-reviewer` agent in PR mode.

## Parse Arguments

Extract from the user's invocation:
- `base` — the base branch or SHA (required)
- `head` — the head branch or SHA (required)
- `--output <path>` — optional; if provided, write the JSON findings artifact to this path

If `base` or `head` is missing, stop and tell the user:

> This skill requires explicit SHAs.
> Usage: `/code-review base=<sha> head=<sha>`
>
> To find your SHAs:
> ```bash
> git log --oneline -5
> # For a GitHub PR: git fetch origin pull/<PR>/head && git rev-parse HEAD origin/main
> ```

## Hand Off to Agent

Invoke the `playwright-typescript-reviewer` agent with:
- `mode: "pr"`
- `base_sha: <base value>`
- `head_sha: <head value>`
- `output_path: <--output value, or null>`

The agent runs the full 7-step pipeline and renders findings to the terminal.
If `output_path` is set, the agent also writes the full JSON schema to that file.
