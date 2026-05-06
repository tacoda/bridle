---
name: finish-branch
description: Verify tests pass, then present merge / PR / keep / discard options and execute the user's choice — worktree-aware
---

Finish the development work on the current branch.

This skill honors `.claude/rules/bridle-mode.md`. The "present options and ask" step is preserved in **all** modes — publishing is a user-owned action.

## IRON LAW

**NO MERGE OR PR WITHOUT GREEN VERIFICATION IN THIS TURN.**

Tests, lint, and build must run *now* and pass. A run from an earlier turn does not count if the diff has changed. See `.claude/rules/verification-before-completion.md`.

## GOLDEN RULES

- **Aim for the smallest publishable unit.** A 200-line PR gets reviewed; a 2,000-line PR gets rubber-stamped or ignored.
- **Aim for a PR body that explains *why*.** What changed is in the diff; the reader needs the motivation.
- **Aim for clean cleanup.** A worktree left over after a merge is mental debt for the next session.

## Workflow

### Phase 1: Verify
1. Run `{{ TEST_COMMAND }}`, `{{ LINT_COMMAND }}`, and `{{ BUILD_COMMAND }}`. Read the output of each.
2. If any fail: stop. Report the failure with evidence. Do not proceed to options. The fix comes first.
3. Confirm `git status` is clean. If dirty, ask the user whether to commit, stash, or discard.

### Phase 2: Detect environment
4. Determine if you are in a worktree:
   ```bash
   GIT_DIR=$(cd "$(git rev-parse --git-dir)" && pwd -P)
   GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" && pwd -P)
   ```
   - If `GIT_DIR != GIT_COMMON` → in a worktree. Cleanup applies.
   - Otherwise → normal checkout.
5. Identify the base branch (default: `{{ MAIN_BRANCH }}`). Get the commit count ahead and behind.

### Phase 3: Present options
6. Show a short summary: branch name, commit count ahead, files changed, test/lint/build status with the evidence from Phase 1.
7. Present a numbered menu:
   1. **Open a PR** — push and run `gh pr create`. Title under 70 chars. Body links to {{ TASK_TRACKER }} and explains the *why*.
   2. **Merge to `{{ MAIN_BRANCH }}`** — fast-forward if possible, otherwise merge commit. Only if the user has explicit merge rights.
   3. **Keep the branch** — leave as-is for further work.
   4. **Discard** — delete the branch and (if applicable) the worktree.
8. Ask the user to choose.

### Phase 4: Execute
9. Run the chosen workflow. Confirm each external action (push, PR creation, merge, delete) before doing it.
10. For PRs: do not mention the AI agent in the body or {{ TASK_TRACKER }} ticket. See `.claude/rules/commits.md`.
11. For merges: pre-commit must have passed in Phase 1. Push after merging.
12. For discards: confirm twice if the branch has unmerged commits.

### Phase 5: Cleanup
13. If you were in a worktree and the branch is now merged or discarded, remove the worktree (`git worktree remove`).
14. Confirm working-tree state is clean and report the final status.

## Key rules

- Tests must pass before options are presented. No exceptions.
- Never force-push to `{{ MAIN_BRANCH }}`. Never delete a branch with unmerged commits without explicit confirmation.
- The user picks the option. You execute it.
