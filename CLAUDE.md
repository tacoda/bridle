# CLAUDE.md

Guidance for Claude Code when working in **bridle**.

## Project Overview

A Claude Code plugin that scaffolds and maintains an agent project harness. The deliverable is markdown (slash commands, agents, skills, rules) plus JSON manifests — no language runtime ships to users. Bridle is the successor to `sellier` (a Python CLI that did the same thing); the templates carry over, the distribution mechanism does not.

**Distribution:** Self-hosted plugin marketplace (separate repo). Users install with `/plugin marketplace add` then `/plugin install bridle@<marketplace>`.

**No test suite.** The product is text files; defects surface at install time. Reviews and self-review are the quality gate.

## Layout

```
.claude-plugin/plugin.json     # plugin manifest
commands/                      # slash commands grouped by harness pillar
agents/                        # plugin-level review agents
skills/                        # multi-phase workflows
templates/                     # what /generate-harness writes into a target project
.claude/                       # bridle's own dogfood harness (this file's siblings)
```

## The Harness

@HARNESS.md

### Bridle's specifics

- Bridle has no automated **Guardrails** — the deliverable is text. Verification is manual review.
- Bridle's commands are organized around the five pillars; the namespace is conveyed by the `description:` field, not the file path.

| Rule file | Scope |
|---|---|
| `.claude/rules/design-principles.md` | Always loaded |
| `.claude/rules/security.md` | Loaded when editing slash commands or scaffolding logic |
| `.claude/rules/commits.md` | Loaded before commit |
| `.claude/rules/scaffolding.md` | Loaded for commands that write into a user's project |

## Change Approval Flow

1. **Self-review** — spawn `self-review` on the diff
2. **Show diff** — present changes and findings, ask for feedback
3. **Feedback given** — update the relevant rule, reload it, re-apply
4. **Approved** — commit and push (conventional commits, no co-authors, no AI attribution)

## Naming

- Commands live under five namespaces matching the harness vocabulary. Files in `commands/` are flat (no subdirs); the namespace is conveyed by the `description:` field and command organization, not the file path.
- Plugin name is `bridle`. Slash commands invoke as `/bridle:<command>`.
