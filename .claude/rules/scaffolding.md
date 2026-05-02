---
description: Safety contract for any command that writes files into a user's project (generate-harness, orient, add-rule, new-skill, new-agent, new-command)
---

# Scaffolding Safety

Bridle commands write into the user's working directory. They must never silently overwrite work the user already has.

## The Contract

For every destination file a command would write:

1. **Destination does not exist** → write the file.
2. **Destination exists and content is byte-identical to the template** → skip silently (no-op).
3. **Destination exists and content differs** → show the diff, then ask the user one of: `skip` / `overwrite`. Default is `skip`.

A command that ignores this contract is a bug.

## CLAUDE.md (special case)

`CLAUDE.md` often holds substantial hand-written content. Stricter rule:

- **No `CLAUDE.md` exists** → write the full template.
- **`CLAUDE.md` exists** → do **not** merge automatically. Present the proposed additions (sections that the harness wants to add) as a numbered list. The user reviews and accepts items individually. Anything not explicitly accepted is skipped.

## settings.json (special case)

`.claude/settings.json` is JSON. Merge keys additively:

- Keys present only in the template → add to the existing file.
- Keys present in both, identical values → leave alone.
- Keys present in both, different values → ask. Default is to keep the user's value.

Never replace the whole file.

## End-of-run report

Every scaffolding command finishes with a one-table summary:

| Path | Action |
|---|---|
| `path/to/file` | wrote / skipped (identical) / skipped (user kept their version) / overwrote |

The user reads the table and knows exactly what changed.

## Why this matters

A scaffolding tool that destroys user work is worse than no tool at all. A new contributor running `/bridle:generate-harness` on a project they don't fully understand must be safe by default.
