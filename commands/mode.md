---
description: Read or set the bridle execution mode (paired, solo, or autopilot)
argument-hint: "[paired|solo|autopilot]"
---

Read or set the bridle execution mode by editing `.claude/rules/bridle-mode.md`.

With no argument, print the current mode. With `paired`, `solo`, or `autopilot`, update the rule and confirm the change.

## Steps

1. **Resolve the rule.** Look for `.claude/rules/bridle-mode.md`.
   - If `.claude/rules/` does not exist, suggest `/bridle:generate-harness` and stop.
   - If the rule is missing but `.claude/rules/` exists, scaffold it from `${CLAUDE_PLUGIN_ROOT}/templates/.claude/rules/bridle-mode.md` and report that the rule was created at the paired default.

2. **Parse `$ARGUMENTS`:**
   - Empty → read the rule, find the `**Active mode: <X>**` line, print the current mode.
   - `paired`, `solo`, or `autopilot` → swap the `**Active mode: <X>**` line to the chosen value. Use exact-string replacement; do not rewrite the rest of the rule.
   - Anything else → print valid options (`paired`, `solo`, `autopilot`) and stop.

3. **Confirm the change.** Print:
   - Before / after of the changed line.
   - One-sentence reminder of what changes:
     - **paired** — agent asks for feedback at every phase boundary.
     - **solo** — agent iterates without asking; stops and reports if anything is unclear.
     - **autopilot** — agent runs end-to-end without asking; logs assumptions; user reviews at the end.
   - Note that mode is per-project and version-controlled with the consumer's `.claude/rules/`.

## Rules

- Mode lives in the consumer's project, not in the plugin. Do not write outside `.claude/rules/bridle-mode.md`.
- This command edits exactly one line. If the rule's structure has been edited by the user, preserve everything else.
- Paired is the default; the rule ships with paired set.
- No mode bypasses the scaffolding safety contract, and no mode auto-publishes commits or pushes — those stay user-driven.
