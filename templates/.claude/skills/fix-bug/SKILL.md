---
name: fix-bug
description: Diagnose and fix a bug using TDD — replicate, gather evidence, diagnose, wrap the bug in a failing test, fix, review, ship
argument-hint: "[brief description of the bug or a tracker reference]"
---

Fix a bug in **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask the user what bug to fix before starting. If the description is unclear, ask for clarification — for bugs, this usually means asking for steps to reproduce, expected vs actual behavior, and any error output or logs.

This skill honors `.claude/rules/bridle-mode.md`. The "ask for feedback" checkpoints below describe **paired** behavior. In **solo**, skip routine feedback asks; stop and report if reproduction details are missing, the diagnosis is uncertain, or the regression test fails for the wrong reason. In **autopilot**, run end-to-end — make a best-effort diagnosis, log it as an assumption, and present it at the final review.

## IRON LAW

**NO FIXES WITHOUT ROOT CAUSE.**

A symptom-fix is a failure regardless of whether the symptom disappears. Until you can name the root cause in one sentence and point at the line of code that produces it, you do not write a fix. Quick patches that mask the issue create the next bug. Read error messages in full, reproduce reliably, trace data flow back to the source.

## GOLDEN RULES

- **Aim for the smallest reliable reproduction.** A bug you can trigger with one command is debuggable; a bug that "sometimes happens in production" is a research project.
- **Aim for evidence at each layer of a multi-component system.** Log what enters and what exits each component before guessing which one is wrong.
- **Aim for a regression test that fails *because of the bug*, not for an adjacent reason.** A test that fails for a syntax error or missing import does not protect against the bug.

## Workflow

### Phase 1: Replicate
1. Reproduce the bug locally. Capture exact steps, inputs, and observed output.
2. Document expected vs actual behavior in one short paragraph.
3. Ask for feedback — is this what the user is seeing?
4. If you cannot reproduce, ask for environment details (version, OS, recent changes, full error) before proceeding. Do not guess.

### Phase 2: Evidence
5. Gather supporting evidence: stack traces, relevant logs, recent commits in the affected area, related tickets.
6. Identify suspect code paths. List files and line ranges.
7. **Multi-component systems:** if the failure crosses component boundaries (CI → build → signing, API → service → database, frontend → backend → cache), add diagnostic instrumentation at each boundary *before* guessing which one is wrong. Log what enters and what exits each layer. Run once. Read the output. Identify which layer fails. Then investigate that one.

### Phase 3: Diagnose
8. Trace the bad value back to its source. The fix goes at the source, not at the symptom.
9. State the root-cause hypothesis in one sentence. If you cannot, you are not done with Phase 2.
10. Validate the hypothesis with a minimal probe (a print, a debugger session, or end-to-end reading of the path).
11. Ask for feedback on the diagnosis. Once approved, proceed.

### Phase 4: Branch
12. Create a branch from latest `origin/{{ MAIN_BRANCH }}` named after the bug (e.g., `fix/<short-description>`).

### Phase 5: TDD — wrap the bug in a test first
13. Write a regression test that fails *because of the bug*. Present the test description for review.
14. Once approved, run the test and confirm it fails for the right reason. A test that fails for the wrong reason is worse than no test.
15. Implement the smallest change that makes the regression test pass.
16. Run the full test suite to confirm nothing else regressed.
17. Show the diff and ask for feedback.

### Phase 6: Review & Commit
18. Spawn the **self-review agent** in parallel with the **review-security agent** (skip security if the bug is purely cosmetic).
19. Combine findings into a single numbered list. Apply clear improvements automatically.
20. Show the full diff and findings; ask for feedback.
21. Once approved, run `/pre-commit`.
22. Commit with conventional `fix:` type, no co-authors, and push.

### Phase 7: CI & Refactor
23. Watch {{ CI_PROVIDER }} in the background — do NOT block.
24. If CI fails, spawn `ci-diagnose`, fix, commit, push.
25. Spawn the **refactor-changes agent** on the diff. Present findings as a numbered list and ask which to address.
26. Run `/pre-commit`, commit, push.

### Phase 8: Done
27. Confirm the bug is fixed (the regression test passes), CI is green, and ask whether to follow up on related issues surfaced during diagnosis. Suggest invoking `finish-branch` if the work is ready to merge or PR.

## Key Rules

- The regression test comes **before** the fix. A bug fix without a failing test that captures it is not done.
- Auto-accept edits during implementation — pause only at the explicit feedback checkpoints.
- Feedback about patterns → update the rule file first, then re-apply.
- Use conventional commits with `fix:` type, no co-authors.
- Do not mention this skill in commit messages, PR descriptions, or {{ TASK_TRACKER }} tickets.
