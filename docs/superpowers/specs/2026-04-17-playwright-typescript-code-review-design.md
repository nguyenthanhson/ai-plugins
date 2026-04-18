# Playwright TypeScript Code Review Plugin Design

Date: 2026-04-17
Status: Draft

## Overview

This design adapts the upstream Bitwarden `bitwarden-code-review` plugin into a local-review-first plugin for Playwright TypeScript automation repositories.

The plugin reviews pull request diffs or local diffs, learns repository-specific coding standards from documentation and nearby code, and returns structured findings to the user locally instead of posting comments to GitHub.

The goal is to produce high-signal review feedback that is tailored to the target repository rather than generic Playwright advice.

## Goals

- Review Playwright TypeScript automation test changes in PR or local diff workflows.
- Infer repository-specific standards before making findings.
- Prioritize reliability and flake prevention while also covering maintainability and Playwright correctness.
- Return structured findings locally for human triage.
- Reuse the proven Bitwarden review pipeline shape where it still fits.

## Non-Goals

- Posting comments directly to GitHub PRs in v1.
- Acting as a general application-code reviewer for mixed product repos.
- Performing broad full-repo scans by default.
- Enforcing generic Playwright style guidance when the local repository has a different documented convention.

## Recommended Approach

Use a Playwright-specialist reviewer with a dedicated repository conventions discovery phase.

This keeps the Bitwarden-style architecture of a main review agent plus focused supporting skills, but changes the review posture in two important ways:

1. The reviewer specializes in automation test repositories using Playwright and TypeScript.
2. The reviewer produces local structured findings instead of writing back to the PR.

This approach best matches the desired balance of reliability, maintainability, and framework correctness while minimizing false positives.

## Architecture

### Plugin

Proposed plugin name:

- `playwright-typescript-code-review`

### Agent

Proposed main agent:

- `playwright-typescript-reviewer`

The agent owns the review workflow end to end:

1. Determine review mode.
2. Gather diff context.
3. Discover repository standards.
4. Analyze changed files with Playwright-specific heuristics.
5. Classify candidate findings.
6. Filter false positives.
7. Render local findings and summary output.

### Review Input Contracts

The reviewer must use deterministic diff inputs so repeated runs on the same code produce the same candidate findings.

#### PR Mode

PR mode should operate on an explicit base SHA and head SHA.

Rules:

- use the provided base SHA and head SHA when available
- compute the comparison diff from `merge-base(base, head)..head`
- treat renamed files according to git rename detection so moved tests retain history-aware context
- review only files included in that resolved diff
- preserve added, modified, deleted, and renamed file status in the internal review input

If PR metadata is unavailable, the command should fail closed or require explicit fallback input rather than silently guessing.

#### Local Mode

Local mode should review a deterministic local diff contract.

Default rules:

- compare `HEAD` to the current working tree
- include both staged and unstaged tracked changes
- exclude untracked files unless the command explicitly opts in
- default to changed files only, with nearby-context expansion handled separately by the standards discovery algorithm

Optional stricter mode may review staged changes only, but that must be an explicit command choice rather than implicit behavior.

#### Include and Exclude Guidance

By default, the reviewer should include:

- `*.spec.ts`
- `*.test.ts`
- Playwright fixtures
- page objects
- Playwright helpers and shared TypeScript testing utilities
- `playwright.config.*`

By default, the reviewer should exclude unrelated generated artifacts, lockfiles, screenshots, traces, videos, and snapshot outputs unless a changed code path directly requires them for context.

### Commands

Proposed commands:

- `code-review`
  - PR-style review flow
- `code-review-local`
  - local diff review flow

### Skills

Proposed supporting skills:

- `discovering-repo-standards`
- `reviewing-playwright-typescript`
- `classifying-review-findings`
- `avoiding-false-positives`
- `rendering-review-findings`
- `rendering-review-summary`

## Repository Standards Discovery

The review should not begin with generic Playwright rules. It should first build a compact internal model of repository conventions.

### Discovery Order

The reviewer should inspect sources in this order:

1. Explicit repository guidance documents
2. Canonical technical configuration
3. Nearby representative code

### Explicit Guidance Documents

Preferred files and locations include:

- `coding-standard.md`
- `coding-standards.md`
- `coding-convention.md`
- `contributing.md`
- `README.md`
- testing guidance under `docs/**`

The reviewer should also look for sections in `README.md` or related docs that describe testing practices, fixture patterns, naming conventions, or preferred selector strategies.

### Canonical Technical Files

Preferred technical sources include:

- `playwright.config.*`
- `package.json`
- lint and formatter configuration
- fixture definitions
- page object files
- shared helpers and utilities

### Nearby Representative Code

When the diff touches tests, fixtures, helpers, or page objects, the reviewer should inspect a bounded set of nearby representative files, typically two to five files, to verify established local patterns.

### Discovery Output

The discovery phase should produce an internal working context containing:

- observed repository conventions
- uncertain conventions
- supporting sources used to infer each convention
- mapping from changed files to nearby context files
- discovery budget usage

### Decision Rules

- Explicit written standards take priority over inferred patterns.
- Repeated local patterns may guide review when documentation is absent.
- If documentation and code disagree, the reviewer should treat the convention as uncertain and avoid overconfident comments.
- The reviewer should avoid claiming a change violates repository standards unless that claim is backed by explicit docs or a clear repeated nearby pattern.

### Conflict Policy

Convention conflicts must be handled deterministically.

Rules:

- if explicit documentation conflicts with local code patterns, do not make a hard "violates repo standard" claim
- downgrade the finding to a low-severity suggestion or explicit uncertainty note unless there is a clear reliability or correctness risk
- include the sources that created the conflict in the finding context or summary
- if the issue is purely stylistic and the convention is conflicted, suppress the finding

### Deterministic Context Selection

Nearby context selection should be deterministic and budgeted.

Selection order for each changed file:

1. explicit documentation files already discovered
2. files imported by the changed file, when they are local fixtures, helpers, or page objects
3. files in the same directory matching the same role as the changed file
4. canonical repository files such as `playwright.config.*`, shared fixtures, and core page-object roots
5. up to two representative sibling tests if more evidence is still needed

Budget rules:

- inspect at most five nearby code files per changed file unless a higher-level command explicitly increases the cap
- cap discovery to a fixed time or token budget per review run
- stop expanding context once the reviewer has enough evidence to classify a convention as explicit, repeated, or uncertain

## Review Heuristics

The plugin should evaluate changed files across three primary lenses.

### 1. Reliability and Flake Prevention

Examples:

- misuse of `waitForTimeout` or hard sleeps
- brittle selectors
- weak or missing assertions
- race-prone navigation or async flows
- hidden shared state across tests
- poor test isolation
- retries masking unstable behavior instead of addressing root cause
- unnecessary `serial` execution or worker assumptions that reduce robustness

### 2. Maintainability and Test Design

Examples:

- poor fixture boundaries
- page object leakage
- duplicated setup or helper logic
- test bodies carrying too much implementation detail
- unclear test names or intent
- utility abstractions that obscure behavior without meaningful reuse

### 3. Playwright and TypeScript Correctness

Examples:

- misuse of locators versus element handles
- incorrect or weak `expect` usage
- failure to rely on Playwright auto-waiting where appropriate
- fixture typing issues
- hook misuse
- configuration mistakes
- unsafe or ambiguous helper typing in reusable TypeScript utilities

## Review Scope

This plugin targets automation-test-only repositories. Because the repository itself is expected to be test-focused, the entire PR can be reviewed through a Playwright TypeScript testing lens.

The reviewer should still stay bounded:

- focus on changed files first
- inspect directly related nearby files for standards and context
- avoid unrelated roaming unless the diff strongly requires broader context

## Output Model

Version 1 is local-review-first.

The plugin should never post inline PR comments or summary comments. Instead, it should present findings directly to the user in a structured local report.

### V1 Output Schema

The review pipeline should use a fixed internal JSON schema in version 1. Rendering and human-facing wording are separate concerns layered on top of this schema.

```json
{
  "review": {
    "mode": "pr|local",
    "diff_base": "string",
    "diff_head": "string",
    "generated_at": "ISO-8601 timestamp",
    "repository_conventions": [
      {
        "id": "string",
        "category": "selector|assertion|waiting|fixtures|page-object|organization|parallelism|typescript|config",
        "summary": "string",
        "source_type": "doc|config|code",
        "sources": ["string"],
        "confidence": "high|medium|low",
        "status": "observed|uncertain|conflicted"
      }
    ],
    "findings": [
      {
        "id": "string",
        "title": "string",
        "severity": "high|medium|low|note",
        "confidence": "high|medium|low",
        "category": "reliability|maintainability|playwright-correctness|typescript-correctness|config",
        "location": {
          "file": "string",
          "line": 1,
          "end_line": 1
        },
        "evidence": {
          "summary": "string",
          "diff_refs": ["string"],
          "convention_refs": ["string"]
        },
        "why_it_matters": "string",
        "suggested_fix": "string",
        "uncertainty_flags": ["string"]
      }
    ],
    "summary": {
      "top_risks": ["string"],
      "assumptions": ["string"],
      "uncertainties": ["string"]
    }
  }
}
```

Schema rules:

- `severity` is required and uses the enum `high|medium|low|note`
- `confidence` is required and uses the enum `high|medium|low`
- `location.file` is required for findings tied to code
- `line` and `end_line` are optional when the issue is file-level or config-level
- `uncertainty_flags` is required and may be empty
- `convention_refs` should point to the repository-standard evidence used by the reviewer

### Output Structure

The output should contain:

1. Findings first
2. Short review summary second

Each finding should include:

- title
- severity
- confidence
- file and line reference when available
- why the issue matters
- suggested fix or direction

The summary should include:

- top risks
- repository conventions inferred
- important uncertainties or assumptions

## Finding Quality Rules

The plugin should favor concrete failure risk over abstract best-practice commentary.

Expected posture:

- explain likely flake or correctness risk clearly
- avoid style-only comments unless tied to a repository standard
- avoid overclaiming when repository conventions are uncertain
- keep low-severity suggestions limited so the output stays high signal

### Confidence Calibration

Confidence must be calibrated rather than treated as free-form intuition.

Definitions:

- `high`
  - explicit repository documentation or repeated local pattern exists, and the diff contains direct evidence of the issue
- `medium`
  - strong local pattern exists without explicit documentation, or the risk is clear but the convention evidence is weaker
- `low`
  - plausible concern exists, but convention evidence or runtime risk is uncertain

Usage rules:

- only `high` and `medium` confidence findings may appear in the main findings list by default
- `low` confidence items should be suppressed or moved into summary uncertainties unless a command explicitly requests exploratory review output
- severity must not be inflated to compensate for low confidence

## Proposed Directory Shape

```text
plugins/playwright-typescript-code-review/
  .claude-plugin/plugin.json
  agents/playwright-typescript-reviewer/AGENT.md
  commands/code-review/
  commands/code-review-local/
  skills/discovering-repo-standards/SKILL.md
  skills/reviewing-playwright-typescript/SKILL.md
  skills/classifying-review-findings/SKILL.md
  skills/avoiding-false-positives/SKILL.md
  skills/rendering-review-findings/SKILL.md
  skills/rendering-review-summary/SKILL.md
  README.md
  CHANGELOG.md
  .claude/settings.json
```

The `.claude/settings.json` file is optional but recommended if we want to define safety boundaries similar to the upstream Bitwarden plugin.

## Open Decisions for Implementation Planning

- Whether findings should also be written to a local artifact file in some modes
- Whether to include optional GitHub read-only tooling for fetching PR metadata
- Whether to reuse any upstream skill wording versus rewriting all skills for the Playwright domain

## Minimum Permissions

The version 1 trust model should be locked to a minimal read-only permission set.

Required capabilities:

- read workspace files
- read git metadata and diffs
- read repository configuration files

Default restrictions:

- no network access by default
- no GitHub write actions
- no local file mutation outside explicitly requested artifact generation
- no PR comment posting
- no destructive git operations

If PR metadata is later supported through GitHub read-only tooling, that integration should remain optional and must not change the local-review-first output model.

## Evaluation and Quality Gates

The design should define measurable success criteria before implementation.

### Benchmark Corpus

Create a small benchmark set of representative Playwright TypeScript diffs:

- reliable-good changes with no expected findings
- flaky-wait regressions
- brittle-selector regressions
- fixture-boundary regressions
- page-object misuse regressions
- TypeScript helper typing regressions
- documented-convention mismatch cases
- doc-versus-code conflict cases

### Target Metrics

Initial version 1 targets:

- high-severity precision of at least 0.90
- medium-severity precision of at least 0.75
- false-positive rate below 0.15 on the benchmark corpus
- convention-match accuracy of at least 0.85 when explicit docs or repeated patterns exist
- median review latency that stays within an agreed local budget for normal PR-sized diffs

### Acceptance Gate

The plugin should not be considered ready for broad use until:

- schema output is stable across repeated runs on the same input
- diff input selection is deterministic
- benchmark precision targets are met
- conflict handling produces no hard repo-standard violation claims in conflicted cases
- low-confidence findings are suppressed or clearly labeled outside the main findings list

## Implementation Direction

The implementation should preserve the upstream plugin's strengths:

- explicit review pipeline
- separation of analysis, classification, and output rendering
- high-signal review posture
- false-positive filtering before final output

The implementation should change the upstream plugin's domain assumptions:

- specialize in Playwright TypeScript automation testing
- learn repository standards before judging changes
- return findings locally instead of posting them remotely

## Summary

This design provides a practical first version of a Playwright TypeScript review plugin that stays close to a proven Bitwarden architecture while adapting the behavior to the needs of automation-test repositories.

The core differentiator is repository-aware review: the plugin should search for documented standards first, confirm them against nearby code, and only then produce findings focused on reliability, maintainability, and framework correctness.
