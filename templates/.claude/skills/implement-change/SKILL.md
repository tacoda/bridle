---
name: implement-change
description: Implement a change (bug fix, feature, follow-up) using TDD — from spec through to a PR
argument-hint: "[brief description of the change, or path to an approved spec]"
---

Implement a change in **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask the user what change to implement before starting. If the description is unclear, ask for clarification before starting.

This skill honors `.claude/rules/bridle-mode.md`. The "ask for feedback" checkpoints below describe **paired** behavior. In **solo**, skip routine feedback asks and iterate; stop only if requirements are ambiguous, rules conflict, or a failure is inscrutable. In **autopilot**, run end-to-end without stopping for ambiguity — note assumptions inline and present them in a structured assumption log at the final review.

## IRON LAWS

**NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST.**

If you find yourself about to write implementation code, stop. Write the test, watch it fail for the right reason, then implement. Code written before its test must be deleted, not adapted. See `.claude/rules/tdd-anti-patterns.md`.

**NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION.**

`{{ LINT_COMMAND }}` and `{{ TEST_COMMAND }}` must pass *in this turn* before you say a phase is done. See `.claude/rules/verification-before-completion.md`.

## GOLDEN RULES

- **Aim for the smallest diff that ships the change.** A 50-line PR ships; a 500-line PR negotiates.
- **Aim for one structural change per commit, separated from behavioral changes.** Refactor, then change behavior — not both at once.
- **Aim for a plan a different engineer could pick up if you walked away mid-flow.** Plans live in `docs/plans/` so the next session has them.

## Workflow

### Phase 0: Spec
1. Check whether `$ARGUMENTS` points at a file in `docs/specs/`. If so, read it and use it as the requirements.
2. Otherwise, decide whether the change needs a spec:
   - **Multi-day effort, ambiguous user intent, or design choices to make** → invoke the `brainstorm` skill first. Do not return here until there is an approved spec at `docs/specs/YYYY-MM-DD-<topic>.md`.
   - **Small, well-understood change** → write a one-paragraph requirements summary inline. Confirm scope with the user before continuing.

### Phase 1: Plan
3. Map out the implementation: files to change, files to add, testing strategy, dependencies.
4. Save the plan to `docs/plans/YYYY-MM-DD-<short-topic>.md` with checkbox-style tasks (`- [ ]` per step). Each step is one action (2–5 minutes): write the failing test, run it, implement, verify, commit.
5. Show the plan to the user and ask for feedback.
6. If the plan has 3+ mostly-independent tasks, suggest invoking `subagent-tasks` to execute it. Otherwise continue here.

### Phase 2: Branch
7. Create a branch from latest `origin/{{ MAIN_BRANCH }}` with a descriptive name.

### Phase 3: TDD
8. Write failing tests. Present test descriptions for review.
9. Once approved, implement the smallest change to make tests pass.
10. Run `{{ TEST_COMMAND }}` and `{{ LINT_COMMAND }}` — confirm both green with the actual command output.
11. Show the diff and ask for feedback.
12. Update the plan file: change `- [ ]` to `- [x]` for completed steps.

### Phase 4: Review & Commit
13. Spawn the **self-review agent** in parallel with the **review-security agent** (skip security if the change is purely cosmetic).
14. Combine findings into a single numbered list. Apply clear improvements automatically.
15. Show the full diff and findings; ask for feedback.
16. Once approved, run `/pre-commit`.
17. Commit (conventional commits, no co-authors, no AI attribution — see `.claude/rules/commits.md`) and push.

### Phase 5: CI & Refactor
18. Watch {{ CI_PROVIDER }} in the background — do NOT block.
19. If CI fails, spawn `ci-diagnose`, fix, commit, push.
20. Spawn the **refactor-changes agent** on the diff. Present findings as a numbered list and ask which to address.
21. Run `/pre-commit`, commit, push.

### Phase 6: Done
22. Confirm everything is green. Suggest invoking `finish-branch` to open a PR or merge.

## Key Rules

- Auto-accept edits during implementation — pause only at the explicit feedback checkpoints.
- Feedback about patterns → update the relevant rule file first, then re-apply.
- The plan file at `docs/plans/` is the source of truth across sessions. Keep its checkboxes current.
- When the plan grows beyond ~5 independent tasks, hand off to `subagent-tasks`.
