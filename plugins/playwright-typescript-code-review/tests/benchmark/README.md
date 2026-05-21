# Benchmark Corpus

Representative Playwright TypeScript diffs for evaluating review quality before marking the acceptance gate as passed.

## Cases

| Case | Expected findings | Purpose |
|------|-------------------|---------|
| `clean-diff` | 0 | Verifies no false positives on well-written Playwright code |
| `flaky-wait` | 2 high (reliability) | Verifies `waitForTimeout` is flagged — two occurrences |
| `brittle-selector` | 2 medium (reliability) | Verifies brittle CSS hash selectors are flagged |
| `fixture-boundary` | 1 medium (maintainability) | Verifies assertion-in-fixture is caught |
| `page-object-misuse` | 2 medium (maintainability) | Verifies assertion methods on page objects are caught |
| `typescript-typing` | 3 low (typescript-correctness) | Verifies Promise<any> return types are flagged |
| `convention-mismatch` | 2 high (reliability) | Verifies explicit doc convention violation is flagged |
| `doc-code-conflict` | 0 findings, 1 uncertainty | Verifies conflicted conventions produce no hard claims |
| `page-pause` | 2 high (reliability) | Verifies page.pause() debugging artifact is caught |
| `floating-promise` | 2 high (playwright-correctness) | Verifies unawaited Playwright API calls are flagged |

## Evaluation Process

Manual scoring. For each case:

1. Run the plugin against the benchmark diff and `conventions.json` as mock discovery output.
2. Compare actual findings to `expected.json`.
3. Score each finding as true positive (TP), false positive (FP), or missed finding (FN).
4. Compute: precision = TP / (TP + FP), recall = TP / (TP + FN).

The acceptance gate requires high-severity precision ≥ 0.90 and medium-severity precision ≥ 0.75 across the full corpus.

## Adding Cases

Each case needs three files:
- `diff.patch` — a realistic `git diff` output for a Playwright TypeScript file
- `conventions.json` — the discovery context the reviewer should use (mock the discovery phase)
- `expected.json` — the v1 schema output the reviewer is expected to produce
