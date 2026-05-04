---
description: Scaffold a new project-local agent in .claude/agents/<name>.md
argument-hint: "[agent name in kebab-case]"
---

Scaffold a new agent named `$ARGUMENTS`. If empty, ask for a name.

## Steps

1. **Validate the name.** Must be kebab-case. If not, propose a corrected version.

2. **Check for collision.** If `.claude/agents/<name>.md` exists, show its contents and ask whether to overwrite (default: no).

3. **Ask five questions:**
   - **Purpose** — what does this agent analyze or own? (one sentence; becomes `description:`)
   - **Shape** — *review agent* (read-only analysis) or *scoped worker* (write-capable, fenced to one tree)?
   - **Scope** — which paths does this agent stay inside? (e.g., `apps/web/**`, `packages/server/**`, `CHANGELOG/**`, or `repo-wide`)
   - **Tools** — which tools does it need? Review agents typically need `Read`, `Grep`, `Glob`, and `Bash(git diff*)`. Scoped workers add `Edit` and `Write`.
   - **Output** — for a review agent, what report shape (numbered list, table, summary)? For a scoped worker, what files does it produce?

4. **Draft the agent file** using the skeleton that matches the chosen shape.

   **Review agent:**

   ```markdown
   ---
   description: <purpose>
   allowed_tools:
     - Read
     - Grep
     - Glob
     - "Bash(git diff*)"
   ---

   <One-paragraph framing — state the scope explicitly: which paths the agent reads and which it stays out of>

   ## Responsibilities
   1. <criterion>
   ...

   ## Output
   <report shape>
   ```

   **Scoped worker:**

   ```markdown
   ---
   description: <purpose>
   allowed_tools:
     - Read
     - Grep
     - Glob
     - Edit
     - Write
   ---

   <One-paragraph framing — state the scope explicitly: which paths the agent owns and which it must not touch>

   ## Responsibilities
   1. <task>
   ...

   ## Output
   <which files it produces or modifies>
   ```

5. **Show the diff** and ask before saving.

## Rules

- Agents fall into two shapes: **review agents** (read-only analyses) and **scoped workers** (write-capable but fenced to one path scope). Pick one shape; don't blur them.
- The point of a subagent is *scoping, not special powers* — a `web` agent should not wander into the server tree.
- Default to the minimum tool surface that gets the job done.
- A scoped worker that wanders outside its declared scope is a bug — restate the scope in the framing so the agent self-checks.
- Follow the safety contract — never silently overwrite.
