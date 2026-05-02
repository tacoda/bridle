---
description: Convert review feedback or a surfaced pattern into a rule update — the core flywheel action
argument-hint: "[brief description of the feedback or pattern]"
---

Convert the feedback in `$ARGUMENTS` into an addition to the harness. If `$ARGUMENTS` is empty, ask: "What's the lesson?"

This is the flywheel's load-bearing command: every time review surfaces a pattern, it should become a rule so the next conversation already knows.

## Steps

1. **Restate the lesson** in one sentence. Confirm with the user.

2. **Identify the home for it:**
   - List `.claude/rules/*.md` with their `description:` fields.
   - Pick the most relevant existing rule.
   - If no existing rule fits, propose creating a new one with `/bridle:add-rule`.

3. **Draft the update.** Show:
   - The current rule body (or "new rule")
   - The proposed addition (specific imperative guidance, anti-patterns if relevant, one concrete example)
   - The full diff

4. **Ask for feedback.** The user may rewrite, narrow the scope, or reject.

5. **Apply** once approved.

6. **Confirm propagation.** State that the next time a matching file is touched, this rule will auto-load. Suggest re-running `/bridle:verify` against the diff to test.

## Rules

- One lesson per `/learn` invocation. Don't bundle unrelated feedback.
- Lessons describe *patterns*. If the feedback is "this specific function is wrong", fix the function — don't write a rule.
- Rules should be falsifiable. Prefer "always do X" or "never do Y" over "consider X".
- If the same lesson keeps coming up across `/learn` invocations, propose extracting it into its own rule via `/bridle:add-rule`.
