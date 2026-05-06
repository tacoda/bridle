---
description: TDD anti-patterns to avoid in {{ PROJECT_NAME }}
paths:
  - "{{ TEST_PATHS }}"
---

# TDD Anti-Patterns

Patterns that look like TDD but undermine it. Catch them in review.

## GOLDEN RULES

- **Aim for tests that read like specifications.** A reviewer should be able to read the test name and body and say what behavior is being asserted, without reading the implementation.
- **Aim for one observable claim per test.** Multiple assertions are fine if they all describe the same single behavior.
- **Aim for tests that fail loudly when the production code is broken**, and only when it is broken.
- **Aim for real collaborators over fakes, fakes over stubs, stubs over mocks** — in that order.

## Writing the test after the code

The most common violation. Code first, test second to "prove" it works.

**Why it fails:** A test that has never failed proves nothing. You cannot tell if it tests the behavior or just mirrors the implementation.

**Fix:** If implementation exists before the test, delete the implementation. Re-write it driven by the test.

## Tests that pass on first run

If your "RED" step never went red, you didn't see the test fail. Either the test is wrong, the implementation already existed, or you're testing the wrong thing.

**Fix:** Always run the test before writing implementation. Confirm it fails *for the right reason* (the assertion, not a syntax error or missing import).

## Mocking internal collaborators

Mocking a function in the same package the test is for. The test now describes call sequences instead of behavior.

**Why it fails:** Coupling tests to call sequences makes refactoring impossible. A clean refactor breaks tests for no reason.

**Fix:** Mock only at system boundaries (HTTP, payment gateway, message queue). Use real internal collaborators.

## Asserting on private state

Tests that read private fields, call private methods, or assert on internal data structures.

**Why it fails:** Locks implementation in. Any restructure breaks the test.

**Fix:** Assert only on observable behavior — return values, public state, observable side effects.

## Snapshot tests as specifications

Pasting whatever the code currently outputs into a snapshot and calling it a spec.

**Why it fails:** A snapshot does not say *why* the output is correct. Drift is invisible — the next "update snapshots" run silently rewrites the spec.

**Fix:** Use snapshots only for output the human reviewer would otherwise hand-verify (rendered UI, complex serialized payloads). Even then, name what the snapshot is asserting.

## Deleting tests to make CI green

A test fails. Instead of fixing the code or fixing the test, the test is deleted or `@skip`'d.

**Why it fails:** Coverage drops silently. The bug the test was guarding against returns.

**Fix:** A failing test is information. Diagnose first. Skip is acceptable only with a tracker link and an owner.

## Over-mocked tests that pass when production is broken

The test mocks the database, the HTTP client, the queue, the time. The "test" is now a tautology — the mocks return what the test expects. Production behavior is never exercised.

**Why it fails:** The test passes; the feature is broken.

**Fix:** Push tests down the pyramid. A unit test with seven mocks should be an integration test with one.

## One test, many assertions

Tests that "test the whole flow" with twelve assertions. The first failure hides the rest.

**Fix:** One logical assertion per test. If you need to assert against multiple aspects of the same act, split into separate tests with shared setup.

## Tests that depend on order

Test B passes only if Test A ran first. Hidden state in module globals, shared fixtures, or external systems.

**Fix:** Each test is independent. Setup in `beforeEach`, teardown in `afterEach`. If global state is unavoidable, isolate the suite.

## `sleep()` in tests

Wait-for-something using a fixed timeout. Flakes under load, slow on every run.

**Fix:** Poll for the condition (with a hard cap), or use deterministic clocks/promises. See `.claude/rules/tests.md`.
