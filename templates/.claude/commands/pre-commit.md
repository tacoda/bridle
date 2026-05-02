Run pre-commit checks based on what changed on the current branch.

## Steps

1. Detect what changed relative to `[MAIN_BRANCH]`:
   ```
   git diff --name-only origin/[MAIN_BRANCH]...HEAD
   ```

2. Run checks based on scope:

   **Always:**
   - `[LINT_COMMAND]`

   **When code changed:**
   - `[TEST_COMMAND]`

   **When frontend changed (omit if N/A):**
   - `[FRONTEND_TEST_COMMAND]`

3. Report each check: passed or failed. If any failed, show the relevant error output. Do NOT proceed to commit — report results only.

## Rules

- Capture output through `tee` into `.test-output/` so failures can be inspected without re-running.
- Fix lint errors with auto-fix when available; re-run lint before reporting.
- Never run multiple test processes in parallel against the same database.
