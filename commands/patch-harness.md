---
description: Patch the consumer harness with new template additions after a bridle upgrade — adds new files and structural changes without rewriting customized templates
argument-hint: "[target directory, defaults to current working directory]"
---

Patch the harness in the project at `$ARGUMENTS` (defaults to the current working directory if empty) with additions and structural changes from the current bridle template tree.

Use this after upgrading the bridle plugin. Unlike `/bridle:generate-harness` (install-time), this command **only** brings forward deltas — it does not re-run customization or re-substitute placeholders.

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

5. Present every **new** file as a numbered list with a one-line summary of its purpose (read from the file's frontmatter `description:` or first heading).
6. Ask the user which to add. Default is "all".
7. Write the accepted files to the consumer's `.claude/` (or root for `HARNESS.md` / etc).

### Phase 3: Placeholder-free existing files

8. For each **placeholder-free existing** file, run `diff` between template and consumer copy.
9. If identical: skip silently.
10. If the consumer's copy is a strict prefix of, subset of, or otherwise a subset of the template (additive upstream change only): show the diff and ask `apply / skip`. Default is `apply`.
11. If the consumer has diverged (their copy has edits not in the template): show the diff and ask `apply (overwrite) / skip / show three-way`. Default is `skip` — diverged files belong to the consumer.

### Phase 4: Placeholder-bearing existing files

12. For each **placeholder-bearing existing** file, do **not** auto-patch. Instead:
    - Generate a "structural diff" — list section headings present in the template but missing from the consumer's copy.
    - List section headings the template has reorganized or renamed.
    - Note any new `IRON LAW` / `GOLDEN RULES` / `## ` headings the consumer lacks.
13. Present the structural diff as advisory output. Tell the user which sections are new upstream so they can decide whether to merge by hand.
14. Do **not** modify these files.

### Phase 5: settings.json

15. JSON-merge as in `/bridle:generate-harness`:
    - Keys only in the template → add.
    - Keys in both, identical → leave.
    - Keys in both, different → ask. Default to keeping the user's value.

### Phase 6: Report

16. Print a single table of every file the command considered:

    | Path | Category | Action |
    |---|---|---|
    | `path/to/file` | new / placeholder-free / placeholder-bearing / settings | added / patched / skipped (identical) / skipped (user kept) / advisory only |

17. If any placeholder-bearing files had structural changes, list them under "Manual review suggested" with a one-line summary each.

---

## Rules

- This command **never** runs placeholder substitution. That belongs to `/bridle:generate-harness` at install time.
- This command **never** modifies a placeholder-bearing file. The consumer owns those copies.
- The contract in `.claude/rules/scaffolding.md` of this plugin still applies — never overwrite without confirmation.
- Do not modify any file outside `CLAUDE.md`, `HARNESS.md`, `GLOSSARY.md`, and `.claude/`.
- New skills, agents, commands, and rules added by an upgrade should appear automatically in Phase 2; no separate scaffolding step is required.
