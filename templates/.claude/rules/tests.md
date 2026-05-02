---
description: Test patterns and TDD workflow for [PROJECT_NAME]
paths:
  - "[TEST_PATHS]"
---

# Tests

Tests are the primary quality gate. Every change must pass `[LINT_COMMAND]` and `[TEST_COMMAND]` before commit.

## Philosophy

Tests verify **behavior, not implementation**. Feed input, check output and observable side effects.

- **Classical/Chicago-school testing.** State-based verification is the default.
- **Fakes and stubs over mocks.** Use real implementations for internal collaborators.
- **Mocks only at system boundaries.** External services (HTTP APIs, payment gateways, message queues) get mocked. The database is NOT a boundary — tests hit it directly via a transactional fixture.
- **Behavior verification is a last resort.** Only assert "X was called" when there is no observable state to check.
- **Never mock internal collaborators.** Mocking internals couples tests to implementation and hides real bugs.

## Test Pyramid

| Tier | Scope | Speed | When to add |
|---|---|---|---|
| Unit | A single class/function | Fast | All new code |
| Integration | Multiple components together | Medium | Cross-component seams |
| Acceptance | Top-level user-facing | Medium | New features (TDD entry point) |
| E2E | Full stack through the runtime | Slow | Major happy paths |

## TDD Workflow

1. **Red** — Write failing tests first. Present test descriptions for review.
2. **Green** — Implement the smallest change to make tests pass.
3. **Refactor** — Clean up only after green tests.
4. **Commit** — Follow the change approval flow in CLAUDE.md.

## Test Naming

Use descriptive names that read as specifications:
- `{actor}_{can|cannot}_{action}_{condition}`
- `{action}_{produces}_{outcome}`

## Arrange-Act-Assert

Structure every test with clear separation. One logical assertion per test.

## What NOT to Do

- Do not test implementation details (private methods, internal state)
- Do not mock internal collaborators
- Do not write tests that depend on execution order
- Do not assert on exact error message strings — assert on status codes and structure
- Do not use `sleep()` — use deterministic waits

## Project-Specific Test Setup

[TEST_SETUP_NOTES]
