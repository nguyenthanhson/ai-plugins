---
name: rendering-review-summary
description: Renders the review summary section — top risks, inferred repository conventions, and suppressed uncertainties. Called by the playwright-typescript-reviewer agent at the end of Step 7, after rendering-review-findings.
---

# Rendering Review Summary

Render the summary section after the findings output. Keep it compact.

## Inputs

- The assembled v1 schema object (the full `review` JSON)

## Format

```
──────────────────────────────────────────────────────────────
SUMMARY
──────────────────────────────────────────────────────────────
```

Then render each non-empty section below. Omit any section that is empty.

### Top Risks

Only render if there are high or medium findings:

```
Top risks:
  • <one sentence naming file/pattern and consequence>
  • <one sentence>
```

Maximum 5 risks. Derive from the high and medium findings — one sentence each naming the file or pattern and the failure consequence.

### Repository Conventions Inferred

Always render if discovery context has any conventions:

```
Repository conventions inferred (<N> total):
  • [<source_type>/<confidence>] <category>: <summary>
  • [<source_type>/<confidence>] <category>: <summary>
```

List all conventions from `review.repository_conventions`.

If any have `status: "conflicted"`, group them under a sub-heading:

```
  Conflicted (excluded from findings):
  • [<source_type>/low] <category>: <summary> (sources: <source1> vs <source2>)
```

### Uncertainties and Suppressed Notes

Only render if `review.summary.uncertainties` is non-empty:

```
Uncertainties and suppressed notes:
  • <note>
  • <note>
```

### Footer

Always render the closing line:

```
──────────────────────────────────────────────────────────────
```

## Output

Render directly to the terminal. No return value needed.
