---
name: code-review-local
description: Review local Playwright TypeScript changes against HEAD for reliability, maintainability, and correctness. Discovers repository standards first. Reviews both staged and unstaged tracked changes by default. Usage — /code-review-local or /code-review-local --staged or /code-review-local --output <path>
---

# Code Review (Local Mode)

Invoke the `playwright-typescript-reviewer` agent in local mode.

## Parse Arguments

Extract from the user's invocation:
- `--staged` — if present, review only staged (cached) changes; default is staged + unstaged tracked changes
- `--output <path>` — optional; if provided, write the JSON findings artifact to this path

No required arguments. Running `/code-review-local` with no arguments reviews all tracked changes against HEAD.

## Hand Off to Agent

Invoke the `playwright-typescript-reviewer` agent with:
- `mode: "local"`
- `staged_only: <true if --staged was passed, false otherwise>`
- `output_path: <--output value, or null>`

The agent runs the full 7-step pipeline and renders findings to the terminal.
If `output_path` is set, the agent also writes the full JSON schema to that file.
