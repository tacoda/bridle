---
description: Add a new rule file to .claude/rules/ for a topic that has surfaced in feedback or review
argument-hint: "[topic name, e.g. logging or background-jobs]"
---

Add a new rule for **$ARGUMENTS**. If `$ARGUMENTS` is empty, ask the user what topic the rule covers.

## Steps

1. **Confirm the topic and scope.** Ask:
   - What pattern should the rule capture?
   - When should it auto-load? (always / specific paths)
   - Why now — what feedback or review surfaced this?

2. **Pick a kebab-case filename** based on the topic (e.g., `logging.md`, `background-jobs.md`).

3. **Check for collision.** If `.claude/rules/<filename>.md` already exists, show its contents and ask whether to:
   - extend the existing rule (recommended) — open it for editing instead
   - pick a different name
   - overwrite (only with explicit confirmation)

4. **Write the rule.** Use this frontmatter shape:

   ```yaml
   ---
   description: <one sentence: what this rule governs>
   paths:
     - "<glob>"   # omit the entire `paths:` field for always-loaded rules
   ---
   ```

   Typical body sections:
   - **Why** — the incident or feedback that motivated the rule
   - **Rule** — the guidance, in imperative voice
   - **Anti-patterns** — what NOT to do
   - **Examples** — concrete cases

5. **Show the diff** and ask for feedback before saving.

## Rules

- Rules describe *patterns*, not one-off fixes. If the lesson is too narrow, fix the code instead.
- Keep rules under ~40 lines. A long rule nobody reads helps nobody.
- Match the voice of existing rules (imperative, concrete, evidence-linked).
- Follow the safety contract in the harness's `.claude/rules/scaffolding.md` — never silently overwrite.
