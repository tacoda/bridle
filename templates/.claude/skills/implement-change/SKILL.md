---
name: implement-change
description: Implement a change (bug fix, feature, follow-up) using TDD — from requirements through to a PR
argument-hint: "[brief description of the change]"
---

Implement a change in **[PROJECT_NAME]**: $ARGUMENTS

If the description is unclear, ask before starting.

## Workflow

### Phase 1: Requirements
1. Restate the problem, expected behavior, and scope as a short requirements doc.
2. Ask for feedback.
3. If feedback references a pattern, update the relevant rule file in `.claude/rules/` first, then revise.

### Phase 2: Implementation Plan
4. Identify files to change, files to add, and the testing strategy.
5. Ask for feedback.

### Phase 3: Branch
6. Create a branch from latest `origin/[MAIN_BRANCH]` with a descriptive name.

### Phase 4: TDD
7. Write failing tests. Present test descriptions for review.
8. Once approved, implement the smallest change to make tests pass.
9. Show the diff and ask for feedback.

### Phase 5: Review & Commit
10. Spawn the **self-review agent** in parallel with the **review-security agent** (skip security if the change is purely cosmetic).
11. Combine findings into a single numbered list. Apply clear improvements automatically.
12. Show the full diff and findings; ask for feedback.
13. Once approved, run `/pre-commit`.
14. Commit (conventional commits, no co-authors) and push.

### Phase 6: CI & Refactor
15. Watch [CI_PROVIDER] in the background — do NOT block.
16. If CI fails, spawn `ci-diagnose`, fix, commit, push.
17. Spawn the **refactor-changes agent** on the diff. Present findings as a numbered list and ask which to address.
18. Run `/pre-commit`, commit, push.

### Phase 7: Done
19. Confirm everything is green and ask for any follow-up.

## Key Rules

- Auto-accept edits during implementation — pause only at the explicit feedback checkpoints.
- Feedback about patterns → update the rule file first, then re-apply.
- Use conventional commits, no co-authors.
- Do not mention this agent in commit messages, PR descriptions, or [TASK_TRACKER] tickets.
