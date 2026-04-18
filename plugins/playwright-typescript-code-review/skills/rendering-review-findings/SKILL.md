---
name: rendering-review-findings
description: Renders the filtered findings list as a structured human-readable terminal report. Called by the playwright-typescript-reviewer agent as the first part of Step 7. Renders findings before the summary.
---

# Rendering Review Findings

Render the filtered findings to the terminal in the format below. The summary is rendered separately by `rendering-review-summary`.

## Inputs

- The assembled v1 schema object (the full `review` JSON)

## Sort Order

1. `high` severity first
2. `medium` severity second
3. `low` severity third
4. `note` last
5. Within each severity tier, sort alphabetically by file path, then by line number ascending

## Header

Always render the header first:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Playwright TypeScript Code Review
Mode: <mode>  Base: <diff_base>  Head: <diff_head>
Generated: <generated_at>
<N> finding(s) — <high-count> high · <med-count> medium · <low-count> low · <note-count> note
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

If there are zero findings:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Playwright TypeScript Code Review
Mode: <mode>  Base: <diff_base>  Head: <diff_head>
Generated: <generated_at>
No findings. Changes look good.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Stop here if there are no findings (do not render an empty findings section).

## Finding Block

For each finding, render:

```
──────────────────────────────────────────────────────────────
[<SEVERITY_LABEL>] [<CATEGORY_LABEL>] — <title>
File: <location.file>:<location.line>–<location.end_line>
Confidence: <confidence>

<why_it_matters>

Suggested fix:
  <suggested_fix>
```

Where file location is read from `location.file`, `location.line`, and `location.end_line` and rendered as:
- `File: <location.file>:<location.line>–<location.end_line>` if both line and end_line are set
- `File: <location.file>:<location.line>` if only line is set
- `File: <location.file>` if neither line nor end_line is set

Where SEVERITY_LABEL is:
- `HIGH` for high severity
- `MED ` for medium (note trailing space for alignment)
- `LOW ` for low (note trailing space for alignment)
- `NOTE` for note

Where CATEGORY_LABEL is:
- `reliability` → `RELIABILITY`
- `maintainability` → `MAINTAINABILITY`
- `playwright-correctness` → `PLAYWRIGHT`
- `typescript-correctness` → `TYPESCRIPT`
- `config` → `CONFIG`

If `uncertainty_flags` is non-empty, append after the suggested fix:

```
⚠ Uncertainty: <comma-separated flags>
```

## Output

Render directly to the terminal. No return value needed.
