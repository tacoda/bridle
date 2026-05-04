---
description: Scaffold a new feature doc in .claude/features/ to capture on-demand context for a domain
argument-hint: "[feature name, e.g. checkout or password-reset]"
---

Scaffold a feature doc for **$ARGUMENTS**. If `$ARGUMENTS` is empty, ask the user which feature the doc covers.

The source template lives at `${CLAUDE_PLUGIN_ROOT}/templates/.claude/features/_template.md`. Read from there — not from the consumer's scaffolded copy — so updates to the template propagate to every project on the next plugin install.

The template uses `<...>` placeholders (copy-time, filled by this command), distinct from bridle's project-wide `{{ }}` placeholders (scaffold-time, filled by `/bridle:generate-harness`). Do not confuse the two.

## Steps

1. **Confirm the feature and scope.** Ask:
   - What capability does this feature implement?
   - Which files, entities, and entry points belong to it?
   - What gotchas have already burned an agent (or a human) on this feature?

2. **Pick a kebab-case filename** based on the feature (e.g., `checkout.md`, `password-reset.md`).

3. **Check for collision.** If `.claude/features/<filename>.md` already exists, show its contents and ask whether to:
   - extend the existing doc (recommended) — open it for editing instead
   - pick a different name
   - overwrite (only with explicit confirmation)

4. **Gather values for each section.** Read the codebase to populate, then propose a table:

   | Section | Proposed value | Source / reason |
   |---|---|---|
   | Title (`<Feature Name>`) | … | … |
   | Description (frontmatter) | … | one-sentence summary |
   | Summary | … | one short paragraph |
   | Key Files | … | bulleted list of `path` — `role` |
   | Domain Terms | … | terms not already in `GLOSSARY.md` |
   | Entities / Data | … | schemas, tables, or core types |
   | Endpoints / Entry Points | … | HTTP routes, CLI commands, queue handlers |
   | Gotchas | … | invariants, ordering, hidden coupling |

   Where evidence is missing, propose `unknown` and ask — never invent.

5. **Substitute and write.** Read the source template, replace each `<...>` placeholder with the approved value, and write to `.claude/features/<filename>.md`. Follow the safety contract in `.claude/rules/scaffolding.md` — never silently overwrite.

6. **Show the diff** and confirm.

## Rules

- Feature docs are **on-demand** context — they are not auto-loaded. Keep them under ~60 lines so the agent can read one without budget anxiety.
- Cite real files and real entry points. A feature doc full of speculation is worse than no doc.
- Domain terms link to `GLOSSARY.md`; don't redefine.
- Substitution happens at command invocation, not at `/bridle:generate-harness` time. Use the `<...>` placeholder shape, not the `{{ }}` shape.
