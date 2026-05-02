---
description: Adjust the path scope of an existing rule so it auto-loads only when relevant files are touched
argument-hint: "[rule name]"
---

Adjust the path scope of the rule named `$ARGUMENTS`.

## Steps

1. **Find the rule.** Look for `.claude/rules/<argument>.md` (try with and without the `.md` suffix). If the file does not exist, list all rules and ask which one.

2. **Show the current scope.** Read the frontmatter:
   - `paths:` present → the rule is path-scoped; list the globs.
   - `paths:` absent → the rule is always-loaded.

3. **Ask the user what the new scope should be:**
   - `always` — remove the `paths:` field entirely.
   - one or more globs (e.g., `tests/**/*.py`, `src/auth/**`).

4. **Update the frontmatter.** Edit only the `paths:` field; preserve everything else.

5. **Show the diff** and confirm.

## Rules

- Path-scope only when the rule is genuinely irrelevant to other files. Path-scoping a project-wide rule weakens it.
- Globs match repo-relative paths.
- Multiple globs in `paths:` are OR'd.
