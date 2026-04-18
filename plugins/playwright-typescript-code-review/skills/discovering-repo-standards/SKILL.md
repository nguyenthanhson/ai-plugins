---
name: discovering-repo-standards
description: Discovers repository-specific Playwright TypeScript coding standards before review begins. Reads explicit documentation, canonical config files, and a deterministically selected bounded set of representative code files to build a compact conventions model. Called by the playwright-typescript-reviewer agent as Step 3 of the review pipeline. Returns a discovery context object.
---

# Discovering Repository Standards

Build a compact internal model of this repository's Playwright TypeScript conventions. Do not start with generic Playwright rules — discovered conventions override defaults during review.

## Inputs

- `changed_files` — list of `{path, status}` objects from the diff
- repository root (current working directory)

## Discovery Budget (hard limits)

- Maximum **5 nearby code files** per changed file
- Maximum **10 code files total** across the entire run
- Explicit documentation files and canonical config files do **not** count toward the 10-file code cap
- Stop expanding when each active convention category is classified as `explicit`, `repeated`, or `uncertain`

## Step 1: Explicit Guidance Documents

Search for documentation files at the repository root and under `docs/`:

```bash
for f in coding-standard.md coding-standards.md coding-convention.md CONTRIBUTING.md contributing.md README.md; do
  [ -f "$f" ] && echo "$f"
done
find docs -name "*.md" -maxdepth 3 2>/dev/null | head -20
```

For each file found, read it and extract any content about:
- **selector strategy** — preferred locator methods, forbidden selector patterns
- **assertion patterns** — preferred matchers, assertion style guidelines
- **waiting strategy** — allowed/forbidden wait patterns (e.g., approved uses of `waitForTimeout`)
- **fixture design** — fixture scope rules, naming conventions, composition patterns
- **page object patterns** — inheritance vs composition, method naming, assertion policy
- **test organization** — describe block structure, test naming conventions
- **parallelism** — worker count policy, `serial` mode usage rules
- **TypeScript patterns** — type usage, generic helper conventions

Record each extracted rule as a convention entry:

```json
{
  "id": "doc-<sequential-number>",
  "category": "<category>",
  "summary": "<exact rule as stated in the doc>",
  "source_type": "doc",
  "sources": ["<filename>"],
  "confidence": "high",
  "status": "observed"
}
```

## Step 2: Canonical Technical Files

Read these files if they exist:

```bash
for f in playwright.config.ts playwright.config.js playwright.config.mts playwright.config.mjs; do
  [ -f "$f" ] && cat "$f" && break
done
cat package.json 2>/dev/null
for f in .eslintrc .eslintrc.json .eslintrc.js .eslintrc.cjs eslint.config.js eslint.config.mjs biome.json; do
  [ -f "$f" ] && cat "$f" && break
done
```

From `playwright.config.*`, extract:
- `testDir` — where tests live (record as `config-testdir`)
- `fullyParallel` and `workers` — parallelism expectations (record as `config-parallelism`)
- `use.baseURL` — base URL pattern if set
- `use.testIdAttribute` — custom test ID attribute if non-default (record as `config-testid-attr`)
- `use.storageState` — authentication setup pattern
- `retries` — retry policy (record as `config-retries`)
- `use.video` — video recording mode

From lint config, extract any Playwright-specific rules such as:
- `playwright/no-wait-for-timeout`
- `playwright/prefer-web-first-assertions`
- `playwright/no-element-handle`

Record each as a convention with `source_type: "config"` and `confidence: "high"`.

## Step 3: Nearby Representative Code

For each changed file, select context files using this priority order (stop when budget is reached):

1. Files imported by the changed file that are local fixtures, helpers, or page objects
   - Parse `import` statements: `grep -n "^import" <changed-file>`
   - Resolve relative paths to actual files
2. Files in the same directory matching the same role (fixture, helper, page object, spec)
   - Use `ls <same-directory>` and filter by naming pattern
3. Root fixture file — look for `fixtures/index.ts`, `fixtures/base.ts`, or a file exporting `test` at the test root
4. Core page object root files — look for `pages/index.ts`, `page-objects/index.ts`
5. Up to two representative sibling test files from the same directory

For each nearby file read, extract repeated patterns:
- How locators are constructed (`getByRole`, `getByTestId`, `getByLabel`, `locator()`, etc.)
- How assertions are written (`toBeVisible`, `toHaveText`, `toContainText`, etc.)
- Whether page objects use inheritance or composition
- How fixtures are defined and scoped (`test.extend`, `{ scope: 'worker' }`)
- Helper function signatures and TypeScript typing patterns

A pattern is `repeated` if observed in **2 or more** nearby code files.
A pattern is `high`-confidence if observed in **3 or more** nearby code files.

Record each as:
```json
{
  "id": "code-<sequential-number>",
  "category": "<category>",
  "summary": "<description of the observed pattern>",
  "source_type": "code",
  "sources": ["<file1>", "<file2>"],
  "confidence": "medium",
  "status": "observed"
}
```

## Conflict Handling

If a `doc` convention and a `code` convention describe the same category but contradict each other:
- Set `status: "conflicted"` on both entries
- Set `confidence: "low"` on both
- Record both source files in `sources`
- Do NOT suppress the entry — record it so the classifier knows to avoid hard repo-standard claims

If no documentation or code evidence exists for a convention category, do not invent one. Leave that category absent from the output.

## Output

Return a discovery context object:

```json
{
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
  "nearby_context_files": {
    "<changed-file-path>": ["<context-file-1>", "<context-file-2>"]
  },
  "discovery_budget_used": 0
}
```
