---
name: worktree
description: Set up an isolated workspace for parallel work — detect existing isolation, create a worktree if needed, run setup, confirm clean baseline
argument-hint: "[branch name or short description of the work]"
---

Set up an isolated workspace for: $ARGUMENTS

If `$ARGUMENTS` is empty, ask the user for a branch name or short description before creating anything.

## Why

Running independent tasks in separate worktrees with separate Claude sessions prevents context cross-talk. See `.claude/rules/session-hygiene.md`.

## IRON LAW

**NO WORKTREE CREATION WITHOUT EXPLICIT CONSENT.**

Worktrees create on-disk state the user has to clean up. Even in autopilot mode, confirm before creating one. Detect existing isolation before assuming you need to create anything.

## GOLDEN RULES

- **Aim for three concurrent worktrees, max.** Beyond that, attention divides faster than parallelism saves time.
- **Aim for the worktree directory next to the main checkout, not inside it.** Nesting confuses tooling and IDE indexers.
- **Aim for a green baseline before reporting ready.** A worktree where lint and tests don't pass at HEAD is a trap for the next session.

## Workflow

### Phase 1: Detect existing isolation
1. Check whether you are already in a worktree:
   ```bash
   GIT_DIR=$(cd "$(git rev-parse --git-dir)" && pwd -P)
   GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" && pwd -P)
   ```
2. If `GIT_DIR != GIT_COMMON`:
   - Verify it is not a submodule: `git rev-parse --show-superproject-working-tree` should return empty.
   - If genuinely in a worktree, report the path and current branch. Skip to Phase 3.
3. If in a normal checkout, continue.

### Phase 2: Create worktree
4. Confirm with the user before creating anything. Show: proposed branch name, worktree directory, base branch.
5. Update the base:
   ```bash
   git fetch origin
   ```
6. Create the worktree:
   ```bash
   git worktree add ../{{ PROJECT_NAME }}-<short-name> -b <branch-name> origin/{{ MAIN_BRANCH }}
   ```
   Use a short, hyphenated name derived from the task description.
7. Switch into the new directory.

### Phase 3: Setup
8. Run the project's install step (e.g., `npm install`, `bundle install`, `composer install`, `pip install -e .`). Detect from `package.json`, `Gemfile`, `composer.json`, `pyproject.toml`, etc.
9. Run `{{ LINT_COMMAND }}` and `{{ TEST_COMMAND }}`. Confirm a clean baseline.
10. If anything fails, stop. Surface the error. Do not report the worktree as ready.

### Phase 4: Report
11. Tell the user the worktree is ready. Include:
    - Worktree directory path.
    - Branch name.
    - Base SHA.
    - Confirmation that lint and tests are green (with the command outputs as evidence).
12. Suggest opening a new Claude session in the worktree directory for the actual work.

## Key rules

- Three concurrent worktrees is a soft ceiling. If the user asks for a fourth, surface the rule from `session-hygiene.md` and confirm.
- Never create a worktree without consent — even in autopilot mode.
- The worktree directory lives **outside** the main checkout (`../`), not inside it.
