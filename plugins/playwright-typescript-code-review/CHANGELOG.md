# Changelog

All notable changes to the Playwright TypeScript Code Review plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0] - 2026-05-21

### Fixed

- Removed duplicate `convention_refs` field from `evidence{}` in candidate finding schema — it was identical to the top-level `convention_refs` and caused confusion about which field downstream skills should reference
- Updated `avoiding-false-positives` Rule 5 suppression message format from stale flat `<file>:<line>` to correct nested `<location.file>:<location.line>`

### Added

- **Lens 1 — Debugging Artifacts**: detect `page.pause()` as a high-severity reliability finding (blocks CI runner indefinitely)
- **Lens 1 — Race-Prone Patterns**: extend `waitForLoadState` coverage to include `'domcontentloaded'` and `'load'` variants, not just `'networkidle'`
- **Lens 2 — Test Body Clarity**: detect tests with more than 6 `await` statements and no `test.step()` grouping as a low-severity maintainability finding
- **Lens 3 — Floating Promises**: detect unawaited Playwright API calls (page.click, locator.fill, expect assertions) as high-severity playwright-correctness findings
- **Lens 3 — expect.soft() Opportunity**: surface as a note when a test has 5+ consecutive independent `expect()` assertions that could benefit from `expect.soft()`
- Benchmark cases: `page-pause` (2 high findings) and `floating-promise` (2 high findings)

## [0.1.2] - 2026-04-20

### Fixed

- Specify full `subagent_type` (`playwright-typescript-code-review:playwright-typescript-reviewer`) in `code-review` and `code-review-local` skills to prevent agent type resolution error

## [0.1.1] - 2026-04-20

### Fixed

- Update plugin structure validation to require flat .md files for agents

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
- Benchmark corpus with eight representative cases covering: clean diff (no findings), flaky waits, brittle selectors, fixture boundary violations, page object misuse, TypeScript typing, documented-convention mismatches, and doc-vs-code conflicts
