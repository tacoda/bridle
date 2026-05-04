---
description: Dashboard of harness health — rule freshness, internal consistency, last learn cadence, and workflow coverage
---

Produce a single-page health report for the project's harness. Read-only.

## Steps

### 1. Rule freshness
- Total rules in `.claude/rules/`
- Rules updated within 30 / 90 / 365 days (count each)
- Rules unchanged for 365+ days (list filenames)

### 2. Workflow coverage
- `.claude/agents/*.md` — count, list filenames
- `.claude/commands/*.md` — count, list filenames
- `.claude/skills/*/SKILL.md` — count, list skill names

### 3. Internal consistency
For each rule, agent, command, and skill:
- Does its frontmatter parse?
- Is `description:` present and non-empty?
- For rules with `paths:`, do any of the globs match zero files in the repo? (orphan scope)
- Do any two rules contradict each other? Look for direct opposites in the body (e.g., one says "always X", another says "never X" in the same context). Flag candidates for human review — do not auto-resolve.

### 4. Flywheel cadence
- Days since the most recent rule modification (proxy for last `/bridle:learn`)
- Number of rule modifications in the last 30 days

### 5. Render the dashboard

```
Harness Health
==============
Rules:        N total / X fresh (<30d) / Y stale (>365d)
Workflows:    A agents / C commands / S skills
Consistency:  ✓ all parse / W warnings (list)
Flywheel:     last update D days ago / N updates in last 30d

Status: 🟢 healthy / 🟡 attention needed / 🔴 stale
```

Status thresholds:
- 🟢 — no warnings, last update <60 days, no rules unchanged 365+ days
- 🟡 — any warning OR last update 60–180 days OR 1–3 rules unchanged 365+ days
- 🔴 — multiple warnings OR last update 180+ days OR 4+ rules unchanged 365+ days

### 6. Recommend one or two actions.
End with a numbered list of suggested next commands (e.g., `/bridle:stale-rules`, `/bridle:retro`).

## Rules

- Read-only. No edits.
- Don't run the review agents — that's `/bridle:verify`. This is meta-state about the harness itself.
- If `.claude/` does not exist, suggest `/bridle:generate-harness` and stop.
