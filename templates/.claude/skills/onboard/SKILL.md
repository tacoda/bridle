---
name: onboard
description: Walk a new engineer through getting set up in this codebase, understanding the layout, and shipping their first trivial change
argument-hint: "[optional: focus area, e.g., 'frontend' or 'payments service']"
---

Onboard a new engineer to **{{ PROJECT_NAME }}**. Optional focus area: $ARGUMENTS.

If `$ARGUMENTS` is non-empty, tailor Phase 1 (Orient) and Phase 3 (Starter task) toward that area. Otherwise, run the generic flow.

This skill **always runs in sync** regardless of `.claude/rules/bridle-mode.md`. The engineer drives the pace; that's the point.

## Workflow

### Phase 1: Orient
1. Read `CLAUDE.md`, `HARNESS.md`, and `README.md`. If any are missing, note it but continue.
2. Summarize the project in 5–7 bullets: tech stack, main branch, task tracker, CI provider, top-level layout, where the domain core lives, and any non-obvious conventions in `CLAUDE.md`.
3. List the top-level directories with one line of purpose each.
4. Ask the engineer if anything is unclear or contradicts what they were told before continuing.

### Phase 2: Verify environment
5. From the project's commands (`{{ LINT_COMMAND }}`, `{{ TEST_COMMAND }}`, `{{ FRONTEND_TEST_COMMAND }}`, `{{ BUILD_COMMAND }}`), figure out the install step (e.g., `npm install`, `bundle install`, `composer install`, `pip install -e .`) and run it.
6. Run `{{ LINT_COMMAND }}` and `{{ TEST_COMMAND }}`. If anything fails or a tool is missing, surface the exact error and **stop**. Do not proceed until the baseline is green — a new engineer has to trust the green-baseline contract.
7. Confirm versions and any OS-specific tooling match what the project expects.

### Phase 3: Pick a starter task and ship it
8. Find candidates the engineer can ship safely:
   - Issues in `{{ TASK_TRACKER }}` labeled "good first issue", "starter", "help wanted", or similar.
   - `TODO` / `FIXME` / `HACK` comments in the codebase, ranked by smallness.
   - Broken or stale snippets in `CLAUDE.md` or `README.md`.
9. Propose 2–3 candidates with a one-line rationale each (why it's small, low-risk, and educational).
10. The engineer picks one. If none fit, ask what they'd rather start with.
11. Hand off to `/implement-change <description>` to ship the change. The engineer drives the conversation in that skill; you support.

### Phase 4: Pointers
12. List the rules in `.claude/rules/` with one-line summaries. Mark which auto-load and which are path-scoped.
13. List the review agents in `.claude/agents/` and when to use each one.
14. List the other skills in `.claude/skills/` and when to invoke them.
15. Tell the engineer how to grow the harness: when a reviewer flags a pattern, run `/learn` (or edit the relevant rule file directly) so the next conversation already knows.

## Key Rules

- Do not proceed past Phase 2 if the test suite is red. A new engineer has to trust the baseline.
- Pause for the engineer's confirmation at each phase boundary. They drive the pace.
- Do not write code on the engineer's behalf in Phase 3 — hand off to `/implement-change` so they own their first PR.
- Do not mention this skill in commit messages, PR descriptions, or {{ TASK_TRACKER }} tickets.
