---
name: classifying-review-findings
description: Assigns final severity and confidence to candidate review findings based on repository conventions evidence and diff evidence. Called by the playwright-typescript-reviewer agent as Step 5 of the review pipeline. Do not inflate severity to compensate for low confidence.
---

# Classifying Review Findings

Assign `severity`, `confidence`, and `uncertainty_flags` to each candidate finding. These values appear in the final output.

## Inputs

- `candidate_findings` — array from Step 4 (reviewing skill)
- `discovery_context` — conventions from Step 3

## Severity Definitions

**high**
The issue will reliably cause test failures, false positives, or mask real regressions.
Examples: `waitForTimeout` in a user-flow test, `ElementHandle` used in an assertion-critical path, missing assertion after navigation in a critical test.

**medium**
The issue will likely cause flakiness or maintainability problems but not guaranteed immediate failure.
Examples: brittle CSS selector in a low-traffic path, page object method containing assertions, mismatched fixture scope.

**low**
Style or design concern with no immediate failure risk.
Examples: unclear test name, minor one-off duplication, helper typed with `any` in an internal utility.

**note**
Informational observation. Not a finding against the code.
Example: "This file uses a different selector strategy than nearby files — not wrong, just inconsistent."

## Confidence Definitions

**high**
Explicit repository documentation OR a repeated local pattern in 3+ files supports the claim, AND the diff contains direct code evidence of the violation.

**medium**
A local pattern in 2 files supports the claim without explicit documentation, OR the Playwright API risk is objectively clear but convention evidence is limited.

**low**
The concern is plausible but convention evidence is absent or the runtime risk is uncertain.

## Assignment Process

For each candidate finding:

**1. Check convention_refs**

Look up each referenced convention ID in `discovery_context.repository_conventions`.

- If any referenced convention has `status: "conflicted"` → set `confidence: "low"` regardless of other evidence
- If any referenced convention has `status: "uncertain"` → cap `confidence` at `"medium"`
- If all referenced conventions have `status: "observed"` and `confidence: "high"` → allow `confidence: "high"` if diff evidence is also direct

**2. Check diff_evidence**

- Direct evidence (the diff explicitly shows the problematic code) → eligible for `"high"` confidence
- Circumstantial evidence (pattern implied but not explicit in the diff) → cap at `"medium"`

**3. Set severity**

Use the severity definitions above based on the failure risk described in `explanation`. Do not adjust severity based on confidence.

**4. Set uncertainty_flags**

Populate the array with any that apply (may be empty):
- `"no-explicit-doc"` — no explicit repository documentation found for this convention; finding relies on inferred code patterns or Playwright API knowledge
- `"conflicted-convention"` — a documentation and code convention for this category contradict each other
- `"single-instance"` — only one nearby code file observed this pattern; not yet a repeated convention
- `"inferred-from-api"` — the risk is based on Playwright API behavior, not any repository-specific convention

## Output

Return the findings list with `severity`, `confidence`, and `uncertainty_flags` added to each item. Preserve all other fields from the candidate finding.
