---
description: Run the project's pre-commit checks (lint, type, tests) scoped to what changed on the current branch
---

Run the project's pre-commit checks against the current branch.

## Steps

1. **Detect what changed** relative to the project's main branch:
   ```
   git diff --name-only origin/$(git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@')...HEAD
   ```

2. **Find the project's check commands.** In order of preference:
   - If `.claude/commands/pre-commit.md` exists → follow its instructions.
   - Else if `package.json` has `scripts.lint` / `scripts.test` → use those.
   - Else if `pyproject.toml` configures a test runner → use it.
   - Else if `Makefile` has `lint` / `test` targets → use those.
   - Else → tell the user no checks are configured; suggest `/bridle:generate-harness` to set up `/pre-commit`.

3. **Run the checks** in this order, stopping on the first failure:
   - Lint (always)
   - Type-check (when configured)
   - Tests (when code files changed)
   - Frontend tests (when frontend files changed)

4. **Report.** For each check:
   - **passed** | **failed** | **skipped**
   - On failure, show only the relevant error output (not the full log).
   - Do NOT proceed to commit — report results only.

## Rules

- Capture output through `tee` into `.test-output/` so the user can inspect failures without re-running.
- Auto-fix lint errors when the linter supports it (e.g., `--fix`). Re-run lint before reporting.
- Never run multiple test processes in parallel against a shared database.
