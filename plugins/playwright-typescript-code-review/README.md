# Playwright TypeScript Code Review

Repository-aware code review for Playwright TypeScript automation test repositories.

Discovers your project's local coding standards first, then reviews diffs for reliability, maintainability, and framework correctness. Returns structured findings locally — never posts to GitHub.

## Installation

```bash
claude plugin add playwright-typescript-code-review github:nguyenthanhson/ai-plugins
```

## Usage

### Review a pull request

```
/code-review base=<base-sha> head=<head-sha>
```

Requires explicit base and head SHAs. To find them:

```bash
git log --oneline -5
# or for a GitHub PR: git fetch origin pull/<PR-number>/head:pr && git rev-parse main pr
```

### Review local changes

```
/code-review-local
```

Reviews all staged and unstaged tracked changes against `HEAD`.

To review staged changes only:

```
/code-review-local --staged
```

### Save findings as a JSON file

Append `--output <path>` to either command:

```
/code-review-local --output review-findings.json
```

## What Gets Reviewed

- `*.spec.ts` and `*.test.ts` files
- Playwright fixture files
- Page objects
- Shared helpers and TypeScript test utilities
- `playwright.config.*`

Generated artifacts (screenshots, traces, videos, lockfiles) are excluded.

## How It Works

1. **Standards discovery** — reads your `coding-standard.md`, `CONTRIBUTING.md`, `playwright.config.*`, and a bounded set of nearby representative code files to learn your project's conventions
2. **Three-lens review** — evaluates changes for reliability/flake risk, maintainability, and Playwright/TypeScript correctness
3. **Confidence calibration** — only surfaces findings backed by your repository's evidence; suppresses low-confidence items and conflicted conventions
4. **Local output** — renders a structured terminal report; no GitHub writes

## Scope

This plugin targets TypeScript-based Playwright automation repositories. It does not review JavaScript-only repos or mixed application+test codebases.

## Permissions

This plugin is read-only. It reads workspace files, git metadata and diffs, and repository configuration. It does not write files (except optional `--output` artifact), does not access the network, and does not post to GitHub.
