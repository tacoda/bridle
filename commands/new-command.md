---
description: Scaffold a new project-local slash command in .claude/commands/<name>.md
argument-hint: "[command name in kebab-case]"
---

Scaffold a new command named `$ARGUMENTS`. If empty, ask for a name.

## Steps

1. **Validate the name.** Must be kebab-case. If not, propose a corrected version.

2. **Check for collision.** If `.claude/commands/<name>.md` exists, show its contents and ask whether to overwrite (default: no).

3. **Ask three questions:**
   - **Purpose** — what does this command do? (one sentence; becomes `description:`)
   - **Arguments** — does it take input? (becomes `argument-hint:`)
   - **Read or write?** — does it modify files? (informs the safety contract section)

4. **Draft the command file** with this shape:

   ```markdown
   ---
   description: <purpose>
   argument-hint: "<hint>"
   ---

   <One-paragraph framing>

   ## Steps

   1. <step>
   ...

   ## Rules
   - <rule>
   ```

5. **Show the diff** and ask before saving.

## Rules

- Commands are single-step utilities. If the work has phases (requirements → tests → review), make it a skill via `/bridle:new-skill`.
- If the command writes files, it must follow the scaffolding safety contract — reference `.claude/rules/scaffolding.md` of this plugin in the command body.
- Follow the safety contract — never silently overwrite an existing command file.
