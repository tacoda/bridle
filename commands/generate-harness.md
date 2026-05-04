---
description: Scaffold the bridle harness into the current project and customize it by replacing placeholders with project-specific values
argument-hint: "[target directory, defaults to current working directory]"
---

Scaffold the bridle harness into the project at `$ARGUMENTS` (defaults to the current working directory if empty).

This command does two things in one pass:

1. **Scaffold** — copy the harness template tree into the target project under the scaffolding safety contract.
2. **Customize** — find every `{{ PLACEHOLDER }}` token in the freshly-scaffolded files and replace it with project-specific values inferred from the codebase, with the user's confirmation.

Phase 1 must complete before Phase 2 starts.

---

## Phase 1: Scaffold

The template tree lives at `${CLAUDE_PLUGIN_ROOT}/templates/`:

```
templates/
├── CLAUDE.md
├── HARNESS.md
└── .claude/
    ├── settings.json
    ├── rules/{design-principles,tests,security,commits,bridle-mode}.md
    ├── agents/{ci-diagnose,refactor-changes,review-functional,review-security,self-review}.md
    ├── commands/pre-commit.md
    └── skills/{implement-change,fix-bug,onboard}/SKILL.md
```

`HARNESS.md` is the canonical definition of the harness pillars. `CLAUDE.md` imports it via `@HARNESS.md`. Treat `HARNESS.md` as a regular template under the scaffolding contract — write if absent, skip if identical, ask if different. It contains no `{{ PLACEHOLDER }}` tokens, so Phase 2 leaves it untouched.

For each template file, follow the contract in `.claude/rules/scaffolding.md` of this plugin:

- **Doesn't exist at destination** → write.
- **Exists, byte-identical** → skip silently.
- **Exists, differs** → show the diff, ask `skip` / `overwrite`. Default is `skip`.

### `CLAUDE.md` — special case

- **No `CLAUDE.md`** → write the full template.
- **`CLAUDE.md` exists** → do not merge. Present the harness sections (Project Overview, The Harness, Commands, Change Approval Flow, TDD Workflow, Pre-Commit Verification) as a numbered list of *proposed additions*. The user accepts items individually. Apply only what they accept; never edit content the user did not approve.

### `.claude/settings.json` — special case

Merge keys additively:
- Keys only in the template → add.
- Keys in both, identical → leave.
- Keys in both, different → ask. Default to keeping the user's value.

Never replace the whole file.

### End of Phase 1

Print a single table summarizing every destination:

| Path | Action |
|---|---|
| `path/to/file` | wrote / skipped (identical) / skipped (user kept their version) / overwrote / merged |

Confirm Phase 1 looks right before moving to Phase 2.

---

## Phase 2: Customize (placeholder substitution)

1. **Inventory placeholders.** Find every `{{ NAME }}` token (uppercase letters, digits, underscores inside double curly braces) in the freshly-scaffolded files only:
   ```
   git grep -nE '\{\{ ?[A-Z][A-Z0-9_]+ ?\}\}' -- CLAUDE.md .claude/
   ```
   The regex tolerates optional spacing so a hand-written `{{NAME}}` is still caught; emit replacements over the spaced form. Build a deduplicated list. Expected tokens (the exact set varies):
   `PROJECT_NAME`, `PROJECT_DESCRIPTION`, `TECH_STACK`, `MAIN_BRANCH`, `TASK_TRACKER`, `CI_PROVIDER`, `LINT_COMMAND`, `TEST_COMMAND`, `BUILD_COMMAND`, `FRONTEND_TEST_COMMAND`, `BUILD_TOOL`, `TEST_PATHS`, `DEPENDENCY_AUDIT_COMMAND`, `PROJECT_LAYOUT`, `PROJECT_PATTERNS`, `TEST_SETUP_NOTES`, `SECURITY_NOTES`, `COMMIT_NOTES`.

2. **Inspect the project for evidence.** Read whichever exist:
   - `package.json`, `pyproject.toml`, `composer.json`, `Cargo.toml`, `go.mod`, `Gemfile` — language and stack
   - `Makefile`, `justfile`, `package.json#scripts` — lint/test/build commands
   - `README.md` — description, getting-started commands
   - `.github/workflows/`, `.gitlab-ci.yml`, `.circleci/` — CI provider
   - `git symbolic-ref refs/remotes/origin/HEAD` — main branch
   - `tests/`, `test/`, `spec/`, `__tests__/` — test paths
   - Top-level directory tree — project layout

3. **Propose values.** Present a table:

   | Placeholder | Proposed value | Source / reason |
   |---|---|---|

   Keep values short and concrete. For descriptive placeholders (`PROJECT_DESCRIPTION`, `PROJECT_LAYOUT`, `PROJECT_PATTERNS`), draft a paragraph or bullet list, not a single line.

4. **Ask for feedback.** The user may correct any value, request more research, or say a placeholder should be removed entirely.

5. **Once approved, replace each token across the freshly-scaffolded files only** (`CLAUDE.md` and `.claude/`). Use exact string replacement — every occurrence of `{{ NAME }}` becomes the chosen value. Replace inside fenced code blocks too. Do not edit any file outside the harness.

6. **Verify.** Re-run the inventory grep. The only matches that should remain are placeholders the user explicitly chose to keep.

7. **Show the diff.** Ask whether the user wants to commit.

---

## Rules

- Do not invent project facts. If evidence is missing, write "unknown" and ask.
- Do not modify any file outside `CLAUDE.md` and `.claude/`.
- Do not add new rule files, agents, commands, or skills as part of this command — use `/bridle:add-rule`, `/bridle:new-agent`, `/bridle:new-command`, `/bridle:new-skill`.
- Never overwrite a file silently. The contract in `.claude/rules/scaffolding.md` is strict.
