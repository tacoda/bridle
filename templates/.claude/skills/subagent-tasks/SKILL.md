---
name: subagent-tasks
description: Execute a multi-task plan by dispatching a fresh subagent per task with two-stage review (spec compliance, then code quality)
argument-hint: "[path to plan file, or empty to use the most recent plan]"
---

Execute a plan in **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, find the most recent plan in `docs/plans/`. If no plan exists, stop and tell the user to run `brainstorm` and `implement-change` first to produce one.

This skill honors `.claude/rules/bridle-mode.md`. Even in paired mode, this skill runs continuously across tasks — the per-task feedback ask is replaced by the two-stage review. The user reviews at the end.

## IRON LAWS

**FRESH SUBAGENT PER TASK. NO INHERITED CONTEXT.**

Each task gets a new subagent. Never reuse a subagent across tasks. Never let a subagent see your session history. The subagent reads only the context you hand it.

**EVERY TASK PASSES TWO REVIEWS BEFORE THE NEXT TASK STARTS.**

Spec-compliance review → code-quality review. A task is not complete until both reviewers approve. Skipping either review is skipping the skill.

**VERIFY THE DIFF, NOT THE SUBAGENT'S SELF-REPORT.**

A subagent saying "done" is not evidence. `git diff` is. See `.claude/rules/verification-before-completion.md`.

## GOLDEN RULES

- **Aim for the minimum context per subagent.** Hand it the task, the relevant rules, the base SHA. Nothing else. More context is more drift.
- **Aim for tasks small enough that a fresh agent can hold them in one head.** If the task description is longer than the implementation, the plan is wrong, not the task.
- **Aim for review findings expressed as code or as concrete file:line references.** "This could be cleaner" is not actionable; "extract lines 42–58 of `foo.py` into a helper" is.

## When to use this instead of `implement-change`

Use `subagent-tasks` when:
- The plan has 3+ tasks that are mostly independent.
- Each task can be specified clearly enough for a fresh subagent to execute without your session's context.
- The full execution can run in one stretch without needing user input mid-flow.

Use `implement-change` for single-task work or tightly-coupled changes where one task informs the next.

## Workflow

### Phase 1: Read the plan
1. Read the plan file. Extract every task with full text, file paths, code blocks, and verification steps.
2. Confirm the task graph: which tasks depend on which others? Note dependencies.
3. Show a short summary (task count, dependency graph, total scope) and ask the user to confirm before starting.

### Phase 2: Per-task loop

For each task, in dependency order:

#### 2a. Dispatch implementer
4. Spawn a fresh `general-purpose` subagent with:
   - The full task text from the plan.
   - The relevant rules in `.claude/rules/` for the files involved (always include `verification-before-completion.md` and `tdd-anti-patterns.md`).
   - The current `git` SHA so the agent works from a known base.
   - Instructions to write tests first (TDD), then code, then run all verification commands (`{{ LINT_COMMAND }}`, `{{ TEST_COMMAND }}`, `{{ BUILD_COMMAND }}`), then `git commit` with a conventional message.
   - Instructions to **not** push.
5. Wait for the implementer to finish. If it asks questions, answer them and re-dispatch.

#### 2b. Spec-compliance review
6. Spawn the **review-functional** agent on the diff with:
   - The original task text.
   - The diff (`git diff <pre-task-sha>..HEAD`).
7. If gaps exist: dispatch the implementer again with the spec-reviewer's findings. Repeat until clean.

#### 2c. Code-quality review
8. Spawn the **self-review** agent (and **review-security** if the task touched auth, queries, file I/O, or response shapes) on the same diff.
9. If issues exist: dispatch the implementer again with the findings. Repeat until clean.

#### 2d. Mark complete
10. Update the plan file: change `- [ ]` to `- [x]` for the completed task. Commit the plan update with `chore(plan):`.

### Phase 3: Final review
11. After all tasks complete, spawn the **self-review** agent on the full branch diff (from the pre-plan SHA to HEAD).
12. Surface findings as a numbered list. Apply clear improvements. Ask the user about judgment calls.
13. Suggest invoking `finish-branch`.

## Key rules

- Each subagent is **fresh** — never reuse a subagent across tasks.
- Each subagent gets **only the context it needs** — never inherit your session.
- Verify the diff yourself; don't trust the subagent's self-report.
- Tests run inside the subagent's task. The pre-commit gate runs there too.
