# CLAUDE.md

Guidance for Claude Code when working in **{{ PROJECT_NAME }}**.

## Project Overview

{{ PROJECT_DESCRIPTION }}

**Tech Stack:** {{ TECH_STACK }}

**Main branch:** `{{ MAIN_BRANCH }}`
**Task tracker:** {{ TASK_TRACKER }}
**CI:** {{ CI_PROVIDER }}

## Layout

{{ PROJECT_LAYOUT }}

---

## The Harness

@HARNESS.md

### This project's specifics

- **Guardrails** for this project: `{{ LINT_COMMAND }}`, `{{ TEST_COMMAND }}`, `{{ FRONTEND_TEST_COMMAND }}`, `{{ BUILD_COMMAND }}`.

| Rule file | Scope |
|---|---|
| `.claude/rules/design-principles.md` | Always loaded — SOLID, KISS, YAGNI, DRY |
| `.claude/rules/bridle-mode.md` | Always loaded — agent pacing (paired / solo / autopilot) |
| `.claude/rules/session-hygiene.md` | Always loaded — clear / compact triggers, worktrees |
| `.claude/rules/verification-before-completion.md` | Always loaded — IRON LAW: evidence before claims |
| `.claude/rules/tests.md` | Loaded when editing tests — TDD workflow, patterns |
| `.claude/rules/tdd-anti-patterns.md` | Loaded when editing tests — anti-patterns to catch |
| `.claude/rules/security.md` | Loaded for auth, queries, file I/O, response shapes |
| `.claude/rules/commits.md` | Loaded before commit — conventional commits, no AI attribution |
| `.claude/rules/mcp.md` | Loaded for `.mcp.json`, `.claude/settings.json` — MCP wiring guidance |
| `.claude/rules/observability.md` | Loaded when editing source code — logs, metrics, traces; IRON LAW: no PII or secrets |
| `.claude/rules/migrations.md` | Loaded for migration files — expand/contract pattern, no destructive changes paired with code |
| `.claude/rules/dependencies.md` | Loaded for manifests / lockfiles — pin, audit, justify additions |
| `.claude/rules/feature-flags.md` | Loaded when adding or changing feature flags — lifecycle and removal discipline |

### Rule conventions

Two kinds of statements appear across these rules:

- **IRON LAWS** are non-negotiable. Violating the letter is violating the spirit. They appear under an `## IRON LAW` (or `IRON LAWS`) heading.
- **GOLDEN RULES** are ideals to aim for. They appear under a `## GOLDEN RULES` heading. Deviation requires reasoning, not permission.

### On-demand context

- `.claude/features/*.md` — feature docs the agent reads when exploring a specific domain. Not auto-loaded; pulled on demand. See `.claude/features/README.md`.
- `GLOSSARY.md` (project root) — domain vocabulary with aliases-to-avoid.
- `docs/specs/YYYY-MM-DD-<topic>.md` — approved design docs produced by the `brainstorm` skill.
- `docs/plans/YYYY-MM-DD-<topic>.md` — implementation plans produced by `implement-change`, executed by `subagent-tasks`.
- `docs/rfcs/NNNN-<slug>.md` — proposals seeking input, produced by the `rfc` skill.
- `docs/adrs/NNNN-<slug>.md` — accepted architectural decisions (ADRs), produced by the `adr` skill.
- `docs/threats/<feature>.md` — threat models produced by the `threat-model` skill.
- `docs/postmortems/YYYY-MM-DD-<slug>.md` — incident postmortems produced by the `postmortem` skill.

### Skills

All skills are runnable manually as `/<name>`. Some are also invoked automatically by other skills, listed in the **Invocation** column.

| Skill | Invocation | Purpose |
|---|---|---|
| `brainstorm` | Standalone; called by `implement-change` (Phase 0) | Turn an idea into an approved spec. Saves to `docs/specs/`. |
| `rfc` | Standalone; routes to `adr` on acceptance | Open a Request for Comments — proposal + alternatives + unresolved questions. Saves to `docs/rfcs/`. |
| `adr` | Standalone; called by `rfc` after acceptance | Capture an architectural decision as an ADR. Saves to `docs/adrs/`. |
| `threat-model` | Standalone | Build a threat model (STRIDE) for a feature. Saves to `docs/threats/`. |
| `implement-change` | Standalone; called by `onboard` for first PR | TDD-driven implementation from spec to PR. Writes a plan to `docs/plans/`. |
| `subagent-tasks` | Standalone; called by `implement-change` for plans with 3+ tasks | Execute a multi-task plan with a fresh subagent per task and two-stage review. |
| `refactor` | Standalone | Behavior-preserving catalog refactor. Small steps, tests after each. |
| `fix-bug` | Standalone | Diagnose and fix a bug; root cause first, regression test before patch. |
| `investigate-perf` | Standalone; recommended by `review-performance` agent | Investigate a perf problem — baseline, profile, change one thing, measure delta. |
| `worktree` | Standalone | Set up an isolated workspace for parallel work. |
| `finish-branch` | Standalone; called by `fix-bug` and `implement-change` | Verify, then open PR / merge / keep / discard. |
| `respond-to-review` | Standalone | Walk through PR review comments, propose fixes, apply, mark resolved. |
| `release` | Standalone | Cut a release — semver bump, changelog, tag, push. |
| `postmortem` | Standalone | Blameless postmortem with timeline + RCA + action items. Saves to `docs/postmortems/`. |
| `onboard` | Standalone | Walk a new engineer through the codebase. |

## Commands

```
{{ LINT_COMMAND }}            # static checks
{{ TEST_COMMAND }}            # full test suite
{{ FRONTEND_TEST_COMMAND }}   # frontend tests (omit if N/A)
{{ BUILD_COMMAND }}           # build / compile
```

## Change Approval Flow

During implementation, auto-accept edits. When changes are ready to commit:

1. **Self-review** — spawn the self-review agent on the diff
2. **Show diff** — present changes and findings, ask for feedback
3. **Feedback given** — update the relevant rule file, reload it, re-apply
4. **Approved** — run `/pre-commit`
5. **Checks pass** — commit and push (conventional commits, no co-authors)

## TDD Workflow

1. **Red** — write failing tests first; present a list of test descriptions for review
2. **Green** — implement the smallest change to make tests pass
3. **Refactor** — clean up only after green; spawn the refactor-changes agent on the diff
4. **Commit** — follow the change approval flow

## Pre-Commit Verification

Run `/pre-commit` immediately before `git commit` — every time. Checks that ran earlier in a task do not count if any code has changed since.
