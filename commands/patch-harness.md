---
description: Patch the consumer harness with new template additions after a bridle upgrade — adds new files and structural changes without rewriting customized templates
argument-hint: "[target directory, defaults to current working directory]"
---

Patch the harness in the project at `$ARGUMENTS` (defaults to the current working directory if empty) with additions and structural changes from the current bridle template tree.

Use this after upgrading the bridle plugin. Unlike `/bridle:generate-harness` (install-time), this command **only** brings forward deltas — it never re-substitutes placeholders in files the consumer already has. New files added by an upgrade are substituted once, before write, so they don't land with raw `{{ }}` tokens.

---

## Scope

Compare the plugin template tree at `${CLAUDE_PLUGIN_ROOT}/templates/` against the consumer's `.claude/`, `CLAUDE.md`, `HARNESS.md`, and `GLOSSARY.md`.

Three categories of files:

1. **Placeholder-bearing templates** (e.g., `CLAUDE.md`, `GLOSSARY.md`, `.claude/rules/security.md`, `.claude/rules/commits.md`, `.claude/rules/tests.md`, `.claude/rules/mcp.md`, `.claude/skills/*/SKILL.md`).
   - The consumer's copies have already had `{{ PLACEHOLDER }}` tokens substituted at install time.
   - **Do not auto-patch these.** Re-applying the template would re-introduce raw `{{ }}` tokens or clobber project-specific text.
   - Action: list them, summarize the upstream changes (additive sections, removed sections, structural moves), and let the user decide whether to manually pull anything in.

2. **Placeholder-free templates** (e.g., `HARNESS.md`, `.claude/rules/design-principles.md`, `.claude/rules/bridle-mode.md`, `.claude/rules/session-hygiene.md`, `.claude/rules/verification-before-completion.md`, `.claude/rules/tdd-anti-patterns.md`, `.claude/agents/*.md`, `.claude/commands/*.md`, `.claude/features/_template.md`, `.claude/features/README.md`).
   - These contain no `{{ PLACEHOLDER }}` tokens, so they are safe to diff and overwrite.
   - Action: 3-way diff. Apply additions, ask before overwriting changes.

3. **New files** that exist in the template tree but not in the consumer's harness.
   - Action: offer to add each one. Default is to add.
   - If a new file contains `{{ PLACEHOLDER }}` tokens, substitute them once before writing — same evidence-gathering + confirm flow as `/bridle:generate-harness` Phase 2, scoped to just the accepted new files. Existing consumer files are never re-substituted.

`.claude/settings.json` is a fourth case — JSON merge, never overwrite. Same as `/bridle:generate-harness`.

---

## Workflow

### Phase 1: Inventory

1. Walk `${CLAUDE_PLUGIN_ROOT}/templates/` and build a list of every file with its relative path.
2. For each file, check whether the consumer has a corresponding file under `CLAUDE.md` / `HARNESS.md` / `GLOSSARY.md` / `.claude/`.
3. Detect whether the template file contains `{{ }}` placeholders:
   ```
   grep -E '\{\{ ?[A-Z][A-Z0-9_]+ ?\}\}'
   ```
4. Classify each file into one of: **new**, **placeholder-bearing existing**, **placeholder-free existing**, **settings.json**.

### Phase 2: New files

5. Present every **new** file as a numbered list with a one-line summary of its purpose (read from the file's frontmatter `description:` or first heading). Mark which contain `{{ PLACEHOLDER }}` tokens.
6. Ask the user which to add. Default is "all".
7. **If any accepted new files contain placeholders**, run substitution before writing:
   - Inventory tokens across the accepted files only:
     ```
     grep -nE '\{\{ ?[A-Z][A-Z0-9_]+ ?\}\}'
     ```
   - For each token, first check whether the consumer's existing harness already binds it. Read `CLAUDE.md`, `GLOSSARY.md`, and `.claude/` looking for the substituted value (e.g., the literal string that filled `{{ TASK_TRACKER }}` last time). If a confident match exists, propose it as the default.
   - For tokens with no prior binding, gather evidence from the project (`package.json`, `pyproject.toml`, `Makefile`, `.github/workflows/`, etc.) the same way `/bridle:generate-harness` Phase 2 does.
   - Present a proposal table:

     | Placeholder | Proposed value | Source |
     |---|---|---|

   - Ask for corrections. Once approved, replace each token across the accepted new files only — never touch existing consumer files.
8. Write the (now-substituted) accepted files to the consumer's `.claude/` (or root for `HARNESS.md` / etc).
9. Re-grep the written files for `{{ }}` — the only matches that should remain are placeholders the user explicitly chose to keep.

### Phase 3: Placeholder-free existing files

10. For each **placeholder-free existing** file, run `diff` between template and consumer copy.
11. If identical: skip silently.
12. If the consumer's copy is a strict prefix of, subset of, or otherwise a subset of the template (additive upstream change only): show the diff and ask `apply / skip`. Default is `apply`.
13. If the consumer has diverged (their copy has edits not in the template): show the diff and ask `apply (overwrite) / skip / show three-way`. Default is `skip` — diverged files belong to the consumer.

### Phase 4: Placeholder-bearing existing files

14. For each **placeholder-bearing existing** file, do **not** auto-patch. Instead:
    - Generate a "structural diff" — list section headings present in the template but missing from the consumer's copy.
    - List section headings the template has reorganized or renamed.
    - Note any new `IRON LAW` / `GOLDEN RULES` / `## ` headings the consumer lacks.
15. Present the structural diff as advisory output. Tell the user which sections are new upstream so they can decide whether to merge by hand.
16. Do **not** modify these files.

### Phase 5: settings.json

17. JSON-merge as in `/bridle:generate-harness`:
    - Keys only in the template → add.
    - Keys in both, identical → leave.
    - Keys in both, different → ask. Default to keeping the user's value.

### Phase 6: Report

18. Print a single table of every file the command considered:

    | Path | Category | Action |
    |---|---|---|
    | `path/to/file` | new / placeholder-free / placeholder-bearing / settings | added / added (substituted) / patched / skipped (identical) / skipped (user kept) / advisory only |

19. If any placeholder-bearing files had structural changes, list them under "Manual review suggested" with a one-line summary each.

---

## Rules

- This command **never** re-substitutes placeholders in files that already exist in the consumer's harness — those copies are owned by the consumer. Substitution runs only on **new** files added in this run, once, before they are written.
- This command **never** modifies an existing placeholder-bearing file. The consumer owns those copies.
- The contract in `.claude/rules/scaffolding.md` of this plugin still applies — never overwrite without confirmation.
- Do not modify any file outside `CLAUDE.md`, `HARNESS.md`, `GLOSSARY.md`, and `.claude/`.
- New skills, agents, commands, and rules added by an upgrade should appear automatically in Phase 2; no separate scaffolding step is required.
