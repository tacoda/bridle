---
description: Build a heatmap of which parts of the codebase carry the most harness-rule debt
---

Cross-reference rules against the codebase and produce a debt heatmap by directory. Read-only.

## Steps

1. **Inventory rules.** Read every `.claude/rules/*.md`. For each, identify the prohibited or required pattern (the "what NOT to do" lines and "always do X" lines).

2. **Walk the codebase.** Spawn one subagent per rule (in parallel, batched in a single call) to count occurrences of the rule's pattern. Each subagent returns:
   - File paths that violate the rule
   - Count per top-level directory

3. **Aggregate by top-level directory.** Build a matrix:

   |  | rule-1 | rule-2 | rule-3 | ... | total |
   |---|---|---|---|---|---|
   | `src/auth/` | 3 | 0 | 5 | | 8 |
   | `src/billing/` | 1 | 2 | 0 | | 3 |
   | ... | | | | | |

4. **Surface the top three pressure points** — directories with the highest total. For each, name the dominant rule and a one-sentence "first move" suggestion (e.g., "extract the duplicated query helpers in `src/auth/`").

5. **Recommend.** One sentence on what to clean up first, why, and which command to run (`/bridle:touch-clean`, `/bridle:smell`, or a manual refactor).

## Rules

- Read-only. No edits.
- Skip non-auditable rules (purely philosophical) and note the count of skipped rules in the output.
- Cap the matrix at 15 directories — group the rest under "other".
