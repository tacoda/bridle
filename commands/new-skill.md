---
description: Scaffold a new project-local skill in .claude/skills/<name>/SKILL.md
argument-hint: "[skill name in kebab-case]"
---

Scaffold a new skill named `$ARGUMENTS`. If empty, ask for a name.

## Steps

1. **Validate the name.** Must be kebab-case (lowercase letters, digits, hyphens). If not, propose a corrected version.

2. **Check for collision.** If `.claude/skills/<name>/SKILL.md` exists, show its contents and ask whether to overwrite (default: no).

3. **Ask three questions:**
   - **Purpose** — what does this skill do? (one sentence; becomes the `description:` field)
   - **Trigger** — when should Claude invoke it? (becomes part of the description)
   - **Arguments** — does it take input? (becomes `argument-hint:`)

4. **Draft `SKILL.md`** with this shape:

   ```markdown
   ---
   name: <name>
   description: <purpose + trigger>
   argument-hint: "<hint>"
   ---

   <One-paragraph framing of what the skill does>

   ## Workflow

   ### Phase 1: <name>
   1. <step>
   ...

   ## Rules
   - <rule>
   ```

5. **Show the diff** and ask before saving.

## Rules

- Skills handle multi-phase workflows. If the work is one step, write a `/bridle:new-command` instead.
- Follow the safety contract — never silently overwrite an existing `SKILL.md`.
