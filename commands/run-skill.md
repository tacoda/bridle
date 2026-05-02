---
description: Manually invoke a project-local skill by name when auto-invocation does not fire
argument-hint: "[skill name] [optional context]"
---

Manually invoke the project-local skill named at the start of `$ARGUMENTS`, passing the rest as context.

Use this when Claude does not auto-invoke a skill that should fit the situation.

## Steps

1. **Parse arguments.** First whitespace-separated token is the skill name; remainder is the context to pass.

2. **Resolve the skill.** Look for `.claude/skills/<name>/SKILL.md`. If absent:
   - List available skills (`.claude/skills/*/SKILL.md`).
   - Ask which one the user meant, or suggest creating it with `/bridle:new-skill`.

3. **Read the skill** and follow its workflow, treating the remaining arguments as `$ARGUMENTS` for that skill.

4. **Run the skill's phases.** Honor every "ask for feedback" checkpoint in the skill — `/bridle:run-skill` does not bypass them.

## Rules

- This is a manual override. If you find yourself running it often for the same skill, the skill's `description:` probably needs to be sharper so auto-invocation fires.
- Pass through arguments verbatim — do not interpret or rewrite them.
