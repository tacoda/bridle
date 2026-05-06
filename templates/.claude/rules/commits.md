---
description: Commit and pull request rules for {{ PROJECT_NAME }}
---

# Commits & Pull Requests

## IRON LAWS

**NO CO-AUTHORS. NO AI ATTRIBUTION.**

Commit messages, PR descriptions, and {{ TASK_TRACKER }} tickets do not mention Claude, an AI agent, or any tool used to author the change. The author is the human running the harness.

**NO COMMITS WITH FAILING PRE-COMMIT CHECKS.**

`{{ LINT_COMMAND }}` and `{{ TEST_COMMAND }}` must pass in this turn before `git commit`. CI is a backstop, not a substitute. See `.claude/rules/verification-before-completion.md`.

## GOLDEN RULES

- **Aim for one logical change per commit.** A reviewer should be able to read the diff in one sitting and explain it back.
- **Aim for structural changes separated from behavioral changes.** A refactor commit and a feature commit on the same diff hide each other.
- **Aim for commit messages that explain *why*, not what.** The diff already says what.

## Conventional Commits

Format: `<type>(<scope>): <subject>`

Common types:
- `feat` — new feature
- `fix` — bug fix
- `refactor` — behavior-preserving structural change
- `test` — adding or improving tests
- `docs` — documentation only
- `chore` — tooling, dependencies, non-code changes

## Rules

- The codebase stays releasable on `{{ MAIN_BRANCH }}` at all times. Use feature flags to decouple deploy from release.
- Never force-push to `{{ MAIN_BRANCH }}` and never bypass pre-commit hooks (`--no-verify`) without explicit user instruction.

## Pull Requests

- Title is short (under 70 characters); details go in the body.
- Body links to the {{ TASK_TRACKER }} ticket and explains the **why**.
- Include a test plan when the change touches user-facing behavior.

## Project-Specific Notes

{{ COMMIT_NOTES }}
