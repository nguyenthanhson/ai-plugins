---
name: reviewing-playwright-typescript
description: Analyzes Playwright TypeScript diff content across three review lenses — reliability and flake prevention, maintainability and test design, and Playwright and TypeScript correctness. Uses the discovery context to apply repository-specific conventions rather than generic defaults. Called by the playwright-typescript-reviewer agent as Step 4. Produces candidate findings before classification and filtering.
---

# Reviewing Playwright TypeScript

Analyze each changed file in the diff across the three lenses below. For each issue found, record a candidate finding. Do not filter by confidence yet — that happens in Steps 5 and 6.

## Inputs

- `diff_content` — the full diff text
- `changed_files` — list of `{path, status}` objects
- `discovery_context` — conventions and nearby context files from Step 3

## Before You Begin

Check the discovery context for relevant conventions in each lens category. If the repository has an explicit convention that differs from the Playwright default, **use the repository convention** — do not flag code that follows the repository's documented approach.

Example: if `discovery_context.repository_conventions` contains a `waiting` convention stating "approved use of `waitForTimeout(500)` after animation completions," do not flag uses that match this description.

---

## Lens 1: Reliability and Flake Prevention

These issues cause intermittent failures or mask genuine regressions. Treat this lens as the highest-priority category.

### Hard Sleeps

Flag:
- `await page.waitForTimeout(<any number>)` — unless a `waiting` convention in the discovery context explicitly approves it for a documented reason
- `await new Promise(resolve => setTimeout(resolve, <n>))` — same rule
- `{ timeout: <value greater than 30000> }` passed as an option without an explanatory comment

### Brittle Selectors

Flag:
- `locator('css=...')` or `.locator('.<class>')` using class names that look auto-generated (contain hashes, random strings, or `_<hash>` suffixes)
- XPath selectors (`.locator('xpath=...')`, `page.$x()`) unless the `selector` convention in the discovery context explicitly approves XPath
- Positional selectors: `:nth-child(<n>)`, `.nth(<n>)` used without a reason tied to a specific ordered list assertion
- If the discovery context has a `config-testid-attr` convention (custom `testIdAttribute`), flag `getByTestId` calls that use the wrong attribute name or use `data-testid` when a different attribute is configured

### Assertion Weaknesses

Flag:
- `expect(await locator.isVisible()).toBe(true)` — use `await expect(locator).toBeVisible()` for auto-retry
- `expect(await locator.isVisible()).toBeTruthy()` — same issue
- `expect(await locator.textContent()).toContain(text)` — use `await expect(locator).toContainText(text)`
- `expect(await locator.textContent()).toBe(text)` — use `await expect(locator).toHaveText(text)`
- `expect(await locator.count()).toBe(n)` — use `await expect(locator).toHaveCount(n)`
- `expect(await locator.getAttribute('href')).toBe(url)` — use `await expect(locator).toHaveAttribute('href', url)`
- Missing assertion after `page.goto()` — the test navigates but never asserts it reached the expected page

### Race-Prone Patterns

Flag:
- `await link.click(); await page.waitForNavigation()` — the `waitForNavigation` call after `click` is a race condition; use `await Promise.all([page.waitForNavigation(), link.click()])`
- Multiple `page.goto()` calls in a single test without documented reason
- `page.waitForLoadState('networkidle')` before every interaction — Playwright's actionability checks make this redundant

### Test Isolation

Flag:
- `test.describe.configure({ mode: 'serial' })` in a test file (not in config) without a documented reason
- `beforeAll` that creates data used directly in individual tests without a matching `afterAll` cleanup
- Tests that depend on execution order (implicit `beforeAll` side effects that subsequent tests rely on)

### Retry Masking

Flag:
- `test.retry(n)` applied to a specific test rather than globally via config — unless the discovery context has a `config-retries` convention that documents per-test retries

---

## Lens 2: Maintainability and Test Design

These issues degrade the codebase over time but do not cause immediate failures.

### Fixture Boundary Issues

Flag:
- A fixture that performs assertions — assertions belong in the test body, not in fixture setup
- A fixture that sets up state for two clearly unrelated scenarios (fixture does too much)
- Worker-scoped fixture (`{ scope: 'worker' }`) used for state that is test-specific (should be default scope)
- Repeated setup code in `beforeEach` blocks across multiple test files that matches what a fixture would do

### Page Object Issues

Flag:
- Page object method that calls `expect()` — assertions belong in the test body
- Page object that imports another page object directly (coupling via direct import instead of being passed as a dependency)
- Page object that has a method longer than ~15 lines with no meaningful sub-operations extracted

### Duplicated Logic

Flag:
- Identical or near-identical `beforeEach` blocks in two or more test files visible in the diff
- The same `page.goto(url)` + login sequence repeated in multiple test files — should be a fixture

### Test Body Clarity

Flag:
- Test with more than 12 `await` statements in the body without page object abstraction
- Test name that describes the action rather than the expected outcome: `test('clicks submit button')` instead of `test('submits form and shows confirmation message')`
- Describe block nesting deeper than 2 levels

### Dead Abstractions

Flag:
- Helper function used in only one test file in the diff — if only one file uses it, consider inlining
- Page object method that wraps a single `locator.click()` with no meaningful abstraction

---

## Lens 3: Playwright and TypeScript Correctness

These are incorrect uses of the Playwright API or TypeScript type system.

### ElementHandle Usage

Flag:
- `page.$()` or `page.$$()` — these return `ElementHandle` which does not auto-retry; replace with `page.locator()`
- `await locator.elementHandle()` called to pass to another Playwright method — usually indicates an unnecessary handle conversion

### Incorrect Expect Usage

Flag:
- Any assertion that `await`s a value before passing to `expect()` when a Playwright-native matcher exists (see Lens 1 assertion list — same patterns apply here for correctness)
- `toBeVisible()` called on a locator that matches multiple elements when the intent is a specific one — should use `.first()`, `.nth(n)`, or a more specific locator

### Auto-Waiting Violations

Flag:
- `page.waitForSelector(selector)` called immediately before `page.locator(selector).click()` — the locator already waits for actionability
- Manually checking `isVisible()` before acting on an element — Playwright auto-waits for visibility

### Fixture Typing

Flag:
- Fixture that returns `any`:
  ```ts
  // bad
  test.extend<{ myFixture: any }>
  // should be a typed interface
  ```
- `test.extend<{}>()` called without the type parameter (drops all type safety)
- Fixture function parameter typed as `any` instead of a proper interface

### Hook Misuse

Flag:
- `beforeEach` inside a `describe` block that contains setup only used by a single test in that block — move to the test itself or use a fixture
- `afterEach` that calls `page.close()` or `context.close()` — Playwright manages browser context lifecycle automatically

### Configuration Issues

Flag (in `playwright.config.*` changes only):
- `retries` set globally to a value greater than 2 without an explanatory comment
- `use.video: 'on'` set globally — this dramatically increases CI storage and runtime; prefer `'on-first-retry'` or `'retain-on-failure'`
- `timeout` set to a value greater than 60000ms without explanation

### TypeScript Typing

Flag:
- Helper function with return type `Promise<any>` where a concrete type is inferable
- `as unknown as T` cast in a reusable utility without a type guard or comment explaining why

---

## Candidate Finding Format

For each issue found, record:

```json
{
  "title": "<one-line description of the specific issue>",
  "category": "reliability|maintainability|playwright-correctness|typescript-correctness|config",
  "raw_severity": "high|medium|low",
  "file": "<file path from the diff>",
  "line": <line number if identifiable from the diff, or null>,
  "end_line": <end line if a range, or null>,
  "diff_evidence": "<the specific diff lines that show the issue>",
  "explanation": "<why this matters — specific risk or consequence>",
  "suggested_fix": "<specific code-level suggestion>",
  "convention_refs": ["<convention id from discovery context if a convention applies, or empty array>"]
}
```

Return the full `candidate_findings` array. Include all severity levels.
