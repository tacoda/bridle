---
name: fix-bug
description: Diagnose and fix a bug using TDD — replicate, gather evidence, diagnose, wrap the bug in a failing test, fix, review, ship
argument-hint: "[brief description of the bug or a tracker reference]"
---

Fix a bug in **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, ask the user what bug to fix before starting. If the description is unclear, ask for clarification — for bugs, this usually means asking for steps to reproduce, expected vs actual behavior, and any error output or logs.

This skill honors `.claude/rules/bridle-mode.md`. The "ask for feedback" checkpoints below describe **paired** behavior. In **solo**, skip routine feedback asks; stop and report if reproduction details are missing, the diagnosis is uncertain, or the regression test fails for the wrong reason. In **autopilot**, run end-to-end — make a best-effort diagnosis, log it as an assumption, and present it at the final review.

## Workflow

### Phase 1: Replicate
1. Reproduce the bug locally. Capture exact steps, inputs, and observed output.
2. Document expected vs actual behavior in one short paragraph.
3. Ask for feedback — is this what the user is seeing?
4. If you cannot reproduce, ask for environment details (version, OS, recent changes, full error) before proceeding. Do not guess.

### Phase 2: Evidence
5. Gather supporting evidence: stack traces, relevant logs, recent commits in the affected area, related tickets.
6. Identify suspect code paths. List files and line ranges.

### Phase 3: Diagnose
7. State the root-cause hypothesis in one sentence.
8. Validate it with a minimal probe (a print, a debugger session, or end-to-end reading of the path).
9. Ask for feedback on the diagnosis. Once approved, proceed.

### Phase 4: Branch
10. Create a branch from latest `origin/{{ MAIN_BRANCH }}` named after the bug (e.g., `fix/<short-description>`).

### Phase 5: TDD — wrap the bug in a test first
11. Write a regression test that fails *because of the bug*. Present the test description for review.
12. Once approved, run the test and confirm it fails for the right reason. A test that fails for the wrong reason is worse than no test.
13. Implement the smallest change that makes the regression test pass.
14. Run the full test suite to confirm nothing else regressed.
15. Show the diff and ask for feedback.

### Phase 6: Review & Commit
16. Spawn the **self-review agent** in parallel with the **review-security agent** (skip security if the bug is purely cosmetic).
17. Combine findings into a single numbered list. Apply clear improvements automatically.
18. Show the full diff and findings; ask for feedback.
19. Once approved, run `/pre-commit`.
20. Commit with conventional `fix:` type, no co-authors, and push.

### Phase 7: CI & Refactor
21. Watch {{ CI_PROVIDER }} in the background — do NOT block.
22. If CI fails, spawn `ci-diagnose`, fix, commit, push.
23. Spawn the **refactor-changes agent** on the diff. Present findings as a numbered list and ask which to address.
24. Run `/pre-commit`, commit, push.

### Phase 8: Done
25. Confirm the bug is fixed (the regression test passes), CI is green, and ask whether to follow up on related issues surfaced during diagnosis.

## Key Rules

- The regression test comes **before** the fix. A bug fix without a failing test that captures it is not done.
- Auto-accept edits during implementation — pause only at the explicit feedback checkpoints.
- Feedback about patterns → update the rule file first, then re-apply.
- Use conventional commits with `fix:` type, no co-authors.
- Do not mention this skill in commit messages, PR descriptions, or {{ TASK_TRACKER }} tickets.
