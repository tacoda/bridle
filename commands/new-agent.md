---
description: Scaffold a new project-local agent in .claude/agents/<name>.md
argument-hint: "[agent name in kebab-case]"
---

Scaffold a new agent named `$ARGUMENTS`. If empty, ask for a name.

## Steps

1. **Validate the name.** Must be kebab-case. If not, propose a corrected version.

2. **Check for collision.** If `.claude/agents/<name>.md` exists, show its contents and ask whether to overwrite (default: no).

3. **Ask three questions:**
   - **Purpose** — what does this agent analyze? (one sentence; becomes `description:`)
   - **Tools** — what does it need access to? Most read-only review agents need only `Read`, `Grep`, `Glob`, and a few `Bash(git diff*)` patterns.
   - **Output** — what shape of report? (numbered list, table, summary)

4. **Draft the agent file** with this shape:

   ```markdown
   ---
   description: <purpose>
   allowed_tools:
     - Read
     - Grep
     - Glob
     - "Bash(git diff*)"
   ---

   <One-paragraph framing>

   ## What to Check
   1. <criterion>
   ...

   ## Output Format
   <shape>
   ```

5. **Show the diff** and ask before saving.

## Rules

- Agents are isolated, read-only analyses. If the work needs to write files, it's a skill or command, not an agent.
- Default to the minimum tool surface that gets the job done.
- Follow the safety contract — never silently overwrite.
