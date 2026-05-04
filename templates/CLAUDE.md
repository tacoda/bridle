# CLAUDE.md

Guidance for Claude Code when working in **{{ PROJECT_NAME }}**.

## Project Overview

{{ PROJECT_DESCRIPTION }}

**Tech Stack:** {{ TECH_STACK }}

**Main branch:** `{{ MAIN_BRANCH }}`
**Task tracker:** {{ TASK_TRACKER }}
**CI:** {{ CI_PROVIDER }}

## Layout

{{ PROJECT_LAYOUT }}

---

## The Harness

@HARNESS.md

### This project's specifics

- **Guardrails** for this project: `{{ LINT_COMMAND }}`, `{{ TEST_COMMAND }}`, `{{ FRONTEND_TEST_COMMAND }}`, `{{ BUILD_COMMAND }}`.

| Rule file | Scope |
|---|---|
| `.claude/rules/design-principles.md` | Always loaded — SOLID, KISS, YAGNI, DRY |
| `.claude/rules/tests.md` | Loaded when editing tests — TDD workflow, patterns |
| `.claude/rules/security.md` | Loaded for auth, queries, file I/O, response shapes |
| `.claude/rules/commits.md` | Loaded before commit — conventional commits |

## Commands

```
{{ LINT_COMMAND }}            # static checks
{{ TEST_COMMAND }}            # full test suite
{{ FRONTEND_TEST_COMMAND }}   # frontend tests (omit if N/A)
{{ BUILD_COMMAND }}           # build / compile
```

## Change Approval Flow

During implementation, auto-accept edits. When changes are ready to commit:

1. **Self-review** — spawn the self-review agent on the diff
2. **Show diff** — present changes and findings, ask for feedback
3. **Feedback given** — update the relevant rule file, reload it, re-apply
4. **Approved** — run `/pre-commit`
5. **Checks pass** — commit and push (conventional commits, no co-authors)

## TDD Workflow

1. **Red** — write failing tests first; present a list of test descriptions for review
2. **Green** — implement the smallest change to make tests pass
3. **Refactor** — clean up only after green; spawn the refactor-changes agent on the diff
4. **Commit** — follow the change approval flow

## Pre-Commit Verification

Run `/pre-commit` immediately before `git commit` — every time. Checks that ran earlier in a task do not count if any code has changed since.
