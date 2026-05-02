---
description: Apply the boy-scout rule — for files touched in the current diff, surface smells and rule violations and propose cleanups
---

For every file changed in the current branch, run reviewers and rule-checks on those files specifically. The principle: if you touched it, leave it cleaner than you found it.

## Steps

1. **Detect touched files.**
   ```
   git diff --name-only origin/$(git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@')...HEAD
   ```
   Filter to files that still exist (skip deletions).

2. **Spawn agents in parallel** on the touched files only:
   - `refactor-changes` — smells specific to touched code
   - `review-functional` — logic issues in touched code
   - For each rule in `.claude/rules/` whose `paths:` matches a touched file, check that file against the rule.

3. **Aggregate findings.** Group by file:

   ### `path/to/file.ext`
   - **smell**: ... (refactor-changes)
   - **logic**: ... (review-functional)
   - **rule violation**: ... (rule-name)

4. **For each file, propose a one-line cleanup** that fits the scope of the current change. Avoid suggesting refactors that would balloon the diff.

5. **Ask the user** which cleanups to apply. Apply only what's accepted.

## Rules

- Read-only by default — only act on user-accepted cleanups.
- Don't propose cleanups that touch files not already in the diff. Boy-scout rule, not gold-plating.
- If a cleanup would more than double the diff size, flag it as "follow-up" rather than "fix now".
