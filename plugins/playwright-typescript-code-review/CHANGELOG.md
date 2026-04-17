# Changelog

All notable changes to the Playwright TypeScript Code Review plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-04-17

### Added

- Initial plugin release
- `playwright-typescript-reviewer` agent with 7-step review pipeline
- `code-review` skill for PR-mode review (requires base SHA and head SHA)
- `code-review-local` skill for local diff review against HEAD
- `discovering-repo-standards` skill — repository conventions discovery with deterministic budget
- `reviewing-playwright-typescript` skill — three-lens review heuristics (reliability, maintainability, correctness)
- `classifying-review-findings` skill — severity and confidence calibration
- `avoiding-false-positives` skill — conflict policy and confidence filtering
- `rendering-review-findings` skill — structured terminal rendering
- `rendering-review-summary` skill — conventions and risk summary
- Optional `--output <path>` argument for JSON artifact export
- Minimal read-only permission set via `.claude/settings.json`
- Benchmark corpus with three representative cases
