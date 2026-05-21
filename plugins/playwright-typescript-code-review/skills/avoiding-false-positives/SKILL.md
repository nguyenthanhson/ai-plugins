---
name: avoiding-false-positives
description: Filters classified findings to remove false positives and low-confidence noise before final rendering. Moves suppressed items to summary uncertainties. Enforces the conflict policy. Called by the playwright-typescript-reviewer agent as Step 6 of the review pipeline.
---

# Avoiding False Positives

Apply filter rules to the classified findings list. The goal is a main findings list where every item is backed by real evidence. Suppressed items are recorded in uncertainties — they are not lost, just demoted.

## Inputs

- `classified_findings` — list from Step 5 with severity, confidence, and uncertainty_flags set
- `discovery_context` — from Step 3

## Filter Rules

Apply all rules in order. A finding suppressed by an earlier rule does not need to be checked by later rules.

### Rule 1: Suppress Low-Confidence Findings

Remove any finding with `confidence: "low"` from the main findings list.
Add to `suppressed_to_uncertainties`: `"[low-confidence] <title> (<location.file>:<location.line>) — suppressed: <one-sentence reason why confidence is low, e.g. 'selector convention is conflicted between CONTRIBUTING.md and existing code patterns'>"`

**Exception:** If `severity` is `"high"` AND `uncertainty_flags` contains only `"no-explicit-doc"` (and NOT `"conflicted-convention"`):
- Keep the finding in the main list
- Prepend `"[Unverified convention] "` to the `title`

### Rule 2: Suppress Conflicted Style Findings

If `uncertainty_flags` contains `"conflicted-convention"` AND `category` is `"maintainability"` AND `severity` is `"low"`:
- Suppress entirely — do not add to uncertainties
- A conflicted style finding is noise

### Rule 3: Rewrite Overconfident Language

For any finding in the main list, scan `why_it_matters` and `title` for these phrases:
- "violates repository standard"
- "against the project convention"
- "contrary to the documented approach"
- "breaks the team convention"

If found, verify:
- At least one `convention_refs` entry has `status: "observed"` AND `confidence: "high"` AND is not `"conflicted"`
- AND `evidence.diff_refs` directly shows the violation

If either condition fails, rewrite the phrase to: `"differs from the observed pattern in <source>"` or `"Playwright recommends <alternative> instead"`.

### Rule 4: Deduplicate

If two findings have the same `location.file` and overlapping `location.line`/`location.end_line` ranges and similar `title` content:
- Keep the finding with higher severity
- If severity is equal, keep the finding with higher confidence
- Keep the `why_it_matters` that is more specific (longer and more precise)
- Drop the other finding (do not add to uncertainties)

### Rule 5: Cap Low-Severity Noise

If there are more than 5 findings with `severity: "low"` or `severity: "note"`:
- Keep the top 3 by this priority: findings with non-empty `convention_refs` first, then by line number
- Move the rest to `suppressed_to_uncertainties`: `"[low-severity] <title> (<location.file>:<location.line>)"`

## Output

Return:

```json
{
  "findings": [...],
  "suppressed_to_uncertainties": ["string", "string"]
}
```

Where `findings` contains only items that passed all filter rules, and `suppressed_to_uncertainties` contains one-line strings for each suppressed item.
