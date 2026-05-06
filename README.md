# bridle

Agent project harness manager for Claude Code. A plugin that scaffolds and maintains the system that lets an AI coding agent produce correct, high-quality code consistently.

Bridle is the plugin successor to [sellier](https://github.com/tacoda/sellier), the Python CLI predecessor. Same templates, simpler install.

## Demo

![bridle demo](https://github.com/user-attachments/assets/ef5bc9e4-2b33-481f-be96-9a1a3db39626)

## Background

- [Announcing sellier](https://dev.to/tacoda/announcing-sellier-4489)
- [Building a harness — standardizing agentic coding in a real codebase](https://dev.to/tacoda/building-a-harness-how-we-standardized-agentic-coding-in-a-real-codebase-4oab)

## Concepts

A **harness** is the system that lets an AI agent produce correct, high-quality code consistently. Bridle's harness has five pillars:

- **Guidance** — `CLAUDE.md`, rules in `.claude/rules/`, on-demand feature docs in `.claude/features/`, and `GLOSSARY.md` for ubiquitous language. Together they shape what the agent writes before it writes a single line.
- **Guardrails** — lint, tests, type-checks the agent runs and won't bypass.
- **Workflows** — agents, commands, and skills as runnable procedures.
- **Flywheel** — review feedback updates rules; rules shape the next conversation.
- **Discipline** — practices that keep the others fresh — debt maps, boy-scout rule, harness health.

The canonical reference is [`HARNESS.md`](HARNESS.md), which is also scaffolded into your project and auto-loaded into context via `@HARNESS.md` from `CLAUDE.md`.

## Install

Recommended: install bridle at the **project level** so every contributor opens the repo with the same harness tooling.

1. Add the marketplace (once per machine):

   ```
   /plugin marketplace add tacoda/tacoda-marketplace
   ```

2. Pin bridle in your project's `.claude/settings.json` and commit it:

   ```json
   {
     "enabledPlugins": {
       "bridle@tacoda-marketplace": true
     }
   }
   ```

Claude Code installs bridle the next time the project opens.

To install for yourself only (no commit), run `/plugin install bridle@tacoda-marketplace` instead.

## Quick start

Open a project in Claude Code:

```
/bridle:generate-harness
```

Scaffolds `CLAUDE.md`, `HARNESS.md` (the canonical definition of the harness pillars), and `.claude/` (rules, agents, skills, commands), then walks the codebase to fill placeholders. Existing files are never silently overwritten — conflicts are shown and confirmed individually.

After the scaffold, you have:

- `CLAUDE.md` — project context loaded on every conversation.
- `HARNESS.md` — pillar definitions.
- `GLOSSARY.md` — ubiquitous domain vocabulary with aliases-to-avoid.
- `.claude/rules/` — behavioral rules (some always-loaded, some path-scoped).
- `.claude/features/` — on-demand domain context, read when exploring a specific feature.
- `.claude/agents/` — review and analysis agents.
- `.claude/commands/` — single-step utilities (e.g., `/pre-commit`).
- `.claude/skills/` — multi-phase workflows for the developer lifecycle: `/brainstorm`, `/rfc`, `/adr`, `/threat-model`, `/implement-change`, `/subagent-tasks`, `/refactor`, `/fix-bug`, `/investigate-perf`, `/worktree`, `/finish-branch`, `/respond-to-review`, `/release`, `/postmortem`, `/onboard`. (See [Scaffolded skills](#scaffolded-skills).)
- `.claude/settings.json` — Claude Code permissions and settings.
- `docs/` — durable artifacts produced by skills: `specs/` (brainstorm), `plans/` (implement-change), `rfcs/`, `adrs/` (ADRs), `threats/`, `postmortems/`. The next session picks up where this one left off.

## Daily workflow

A typical day-to-day loop with bridle in your project:

1. **Pick up a task.** Invoke the right skill: `/brainstorm <idea>` for design conversations, `/implement-change <description>` for features, `/fix-bug <description>` for bugs, `/onboard` for new contributors.
2. **For non-trivial work, brainstorm first.** `/brainstorm` runs Socratic clarification, proposes 2–3 approaches, and writes an approved spec to `docs/specs/YYYY-MM-DD-<topic>.md`. `/implement-change` then reads the spec and writes a plan to `docs/plans/`.
3. **Plans with 3+ independent tasks → `/subagent-tasks`.** Dispatches a fresh subagent per task with two-stage review (spec compliance, then code quality), then verifies the diff yourself.
4. **The skill runs phases** — TDD, review, commit. In **paired** mode (default), it pauses for your feedback at each phase. In **solo**, it iterates autonomously and stops on ambiguity. In **autopilot**, it runs end-to-end and you review at the end. (See [Mode](#mode).)
5. **Before commit, `/pre-commit`** runs lint / tests / type-check. The agent fixes failures rather than bypassing them.
6. **When the work is ready to ship, `/finish-branch`.** Verifies tests pass, then presents merge / PR / keep / discard options.
7. **When reviewers leave comments on the PR, `/respond-to-review`.** Walks each thread, proposes a fix or a counter-reply, applies with your confirmation.
8. **When a reviewer flags a pattern, run `/bridle:learn`** (or edit the relevant rule directly). The next conversation already knows.

For parallel work, `/worktree <name>` sets up an isolated workspace on a new branch with a verified-green baseline.

### Other workflows

The lifecycle covers more than the build loop:

- **`/rfc`** for proposals seeking input. **`/adr`** to record an architectural decision after the proposal lands.
- **`/threat-model`** before implementing a security-sensitive feature — STRIDE walkthrough, mitigations land in the spec.
- **`/refactor`** for behavior-preserving structural change. No behavior change in the same commit; tests run after every catalog step.
- **`/investigate-perf`** when something is slow. Baseline → profile → change one thing → measure delta. No perf claim without before/after numbers.
- **`/release`** to cut a release — semver bump, changelog entry, tag, push, all in one `chore(release):` commit.
- **`/postmortem`** after an incident. Timeline, RCA (5-whys / causal chain), action items that change the *system*. Saved to `docs/postmortems/`.

Less frequent maintenance:

- `/bridle:harness-health` — single-page report on rule freshness and consistency.
- `/bridle:stale-rules` — flag rules unchanged for too long.
- `/bridle:retro` — mine recent commits for patterns worth turning into rules.
- `/bridle:audit` — walk the codebase against every rule.

## Mode

Bridle has three execution modes that change how the agent paces work, nothing else. The names sit on a spectrum of agent autonomy:

| Mode | Asks during work? | Stops on ambiguity? |
|---|---|---|
| `paired` (default) | yes — every phase boundary | n/a (always asks) |
| `solo` | no | yes — stop and report |
| `autopilot` | no | no — best-effort, log assumptions, review at end |

- **paired** — pair programming. You and the agent work together; it pauses for your feedback at each phase.
- **solo** — the agent works independently but raises a hand on hard problems (ambiguous requirements, conflicting rules, inscrutable test failures).
- **autopilot** — fully autonomous. The agent runs end-to-end and surfaces every assumption in a structured log at the final review.

```
/bridle:mode               # print current mode
/bridle:mode paired        # pair at every phase (default)
/bridle:mode solo          # iterate; stop on ambiguity
/bridle:mode autopilot     # run end-to-end; review the diff and assumption log at the end
```

Mode lives in `.claude/rules/bridle-mode.md` — version-controlled with your project, so a team can settle on a default. **No mode** bypasses the scaffolding safety contract or auto-publishes commits — those stay user-driven across all three.

`/onboard` always runs in paired mode regardless of the active mode, because the engineer drives the pace; that's the point.

## You own your harness

The files bridle scaffolds live in your repo, not inside the plugin. Once they're in place, the harness runs on Claude Code alone — bridle's commands just make maintaining it easier.

- **Version-controlled.** `CLAUDE.md` and `.claude/` ship with your code. Diff it, branch it, review it like any other source.
- **Works without bridle.** Uninstall the plugin and the harness keeps working — Claude Code reads `.claude/` natively. Bridle is the tool that maintains the harness, not a runtime dependency of it.
- **No hidden copies.** Bridle's commands operate on the files in your repo, not on duplicates cached inside the plugin. The plugin is the engine; your `.claude/` is the configuration.
- **Editable by hand.** Open the markdown, change a rule, commit. The flywheel commands pick up your edits on the next run.

## Commands

All commands invoke as `/bridle:<command>`.

| Pillar | Command | Description |
|---|---|---|
| harness | `/generate-harness` | Scaffold the harness into the current project and substitute placeholders |
| harness | `/patch-harness` | Bring forward template additions after a bridle upgrade — adds new files, diffs structural changes, never re-runs placeholder substitution |
| harness | `/mode` | Read or set the bridle execution mode (paired, solo, autopilot) |
| guidance | `/add-rule` | Create a new rule file in `.claude/rules/` |
| guidance | `/add-feature-doc` | Scaffold a new on-demand feature doc in `.claude/features/` |
| guidance | `/scope-rule` | Adjust an existing rule's `paths:` scope |
| guidance | `/show-rules` | List every rule with description, scope, and last-modified date |
| guidance | `/orient` | Walk the codebase and propose context additions to `CLAUDE.md` |
| guardrails | `/check` | Run lint, type-check, and tests scoped to the current diff |
| guardrails | `/verify` | Run `/check` plus parallel functional and security review agents |
| guardrails | `/smell` | Spot refactoring opportunities in the current diff |
| guardrails | `/audit` | Walk the codebase against every rule and report violations |
| flywheel | `/learn` | Convert review feedback into a rule update |
| flywheel | `/retro` | Mine recent commits for patterns that should become rules |
| flywheel | `/stale-rules` | Flag rules that haven't been updated and propose actions |
| workflows | `/new-skill` | Scaffold a new project-local skill |
| workflows | `/new-agent` | Scaffold a new project-local agent — review (read-only) or scoped worker (write-capable, fenced to a path tree) |
| workflows | `/new-command` | Scaffold a new project-local slash command |
| workflows | `/run-skill` | Manually invoke a project-local skill by name |
| workflows | `/spawn-reviewers` | Fan out every review agent on the current diff |
| workflows | `/fan-out` | Fan out one agent in parallel over N independent items |
| workflows | `/ci-status` | Show the latest CI runs for the current branch |
| discipline | `/debt-map` | Heatmap of where rule debt is concentrated |
| discipline | `/touch-clean` | Boy-scout rule: surface smells in files in the current diff |
| discipline | `/harness-health` | Dashboard of rule freshness, coverage, and flywheel cadence |

## Scaffolded skills

`/bridle:generate-harness` writes these multi-phase skills into `.claude/skills/`. They're invoked as `/<name>` from your project, run on Claude Code alone, and are yours to edit.

Every skill is runnable manually. Some are also invoked automatically by other skills — the **Invocation** column shows when that happens.

| Skill | Invocation | Description |
|---|---|---|
| `/brainstorm` | Standalone; called by `implement-change` (Phase 0) | Turn a rough idea into an approved spec — Socratic dialogue, 2–3 approaches, writes to `docs/specs/` |
| `/rfc` | Standalone; routes to `adr` on acceptance | Open a Request for Comments — motivation, detailed design, drawbacks, alternatives, unresolved questions. Saves to `docs/rfcs/` |
| `/adr` | Standalone; called by `rfc` after acceptance | Capture an architectural decision as an ADR — context, options, decision, consequences. Saves to `docs/adrs/` |
| `/threat-model` | Standalone | STRIDE-based threat model for a feature, scoped to assets and trust boundaries. Saves to `docs/threats/` |
| `/implement-change` | Standalone; called by `onboard` for first PR | Implement a change using TDD — spec through to a PR; writes a durable plan to `docs/plans/` |
| `/subagent-tasks` | Standalone; called by `implement-change` for plans with 3+ independent tasks | Execute a multi-task plan with a fresh subagent per task and two-stage review |
| `/refactor` | Standalone | Behavior-preserving catalog refactor — small steps, tests after each step, no behavior change in the same commit |
| `/fix-bug` | Standalone | Diagnose and fix a bug using TDD — root cause first, regression test before patch, ship |
| `/investigate-perf` | Standalone; recommended by `review-performance` agent | Investigate a performance problem — baseline, profile, change one thing, measure delta. No perf claims without before/after numbers |
| `/worktree` | Standalone | Set up an isolated workspace for parallel work — detects existing isolation, verifies a green baseline |
| `/finish-branch` | Standalone; called by `fix-bug` and `implement-change` | Verify tests, then present merge / PR / keep / discard options and execute the choice |
| `/respond-to-review` | Standalone | Walk through open PR review comments, propose fixes, apply with confirmation, mark threads resolved |
| `/release` | Standalone | Cut a release — semver bump, changelog entry, tag, push, with one `chore(release):` commit |
| `/postmortem` | Standalone | Blameless postmortem with timeline, impact, root cause analysis (5-whys / causal chain), and action items. Saves to `docs/postmortems/` |
| `/onboard` | Standalone | Walk a new engineer through orient, environment verification, and a first starter PR |

## Scaffolded rules

Rules in `.claude/rules/` are short markdown files Claude Code loads as guidance. Some are always loaded; others auto-load when their scope matches the files being touched.

| Rule | Scope | Topic |
|---|---|---|
| `design-principles.md` | Always | SOLID, KISS, YAGNI, DRY, deep modules, Tell-Don't-Ask |
| `bridle-mode.md` | Always | Agent pacing — paired / solo / autopilot |
| `session-hygiene.md` | Always | When to `/clear` or `/compact`; worktrees for parallel work |
| `verification-before-completion.md` | Always | IRON LAW: no completion claims without fresh verification evidence |
| `tests.md` | Test paths | TDD workflow and patterns |
| `tdd-anti-patterns.md` | Test paths | Catalog of TDD anti-patterns with diagnoses and fixes |
| `security.md` | Auth / queries / I/O / response shapes | OWASP-aligned guidance |
| `commits.md` | Before commit | Conventional commits, IRON LAW: no AI attribution, no failing pre-commit |
| `mcp.md` | `.mcp.json`, `.claude/settings.json` | MCP wiring guidance |
| `observability.md` | Source files | Logs, metrics, traces; IRON LAW: no PII or secrets |
| `migrations.md` | Migration paths | Expand/contract pattern; IRON LAW: no destructive change paired with code that depends on it |
| `dependencies.md` | Manifests / lockfiles | Pin, audit, justify; supply-chain hygiene |
| `feature-flags.md` | Flag config | Lifecycle, types, IRON LAW: every flag has a removal plan at birth |

## Scaffolded agents

Agents in `.claude/agents/` are isolated, often read-only analyses. Skills spawn them in parallel; you can also invoke them via `/spawn-reviewers` or directly.

| Agent | Description |
|---|---|
| `self-review` | Last-mile review of the diff before commit — design, tests, edge cases |
| `review-functional` | Functional correctness review of the diff |
| `review-security` | OWASP-aligned security review of the diff |
| `review-performance` | Performance review of the diff — flags hot-path regressions, N+1s, allocation hotspots, blocking I/O |
| `refactor-changes` | Surfaces refactoring opportunities in the diff |
| `ci-diagnose` | Reads CI logs to diagnose failures |

### Iron Laws and Golden Rules

The scaffolded rules and skills use two distinct rhetorical markers:

- **IRON LAW** — non-negotiable. Violating the letter is violating the spirit. Examples: no production code without a failing test first; no fixes without root cause; no completion claims without fresh verification evidence; no co-authors or AI attribution in commits.
- **GOLDEN RULES** — ideals to aim for. Examples: aim for the smallest publishable diff; aim for tests that read like specifications; aim for the minimum context per subagent.

Iron laws appear under an `## IRON LAW` heading. Golden rules appear under a `## GOLDEN RULES` heading. Both are version-controlled with your project — edit them as your team's standards evolve.

## Safety

Every command that writes files into your project follows a strict contract:

- Never silently overwrites.
- Conflicts are shown as diffs; default action is **skip**.
- `CLAUDE.md` is propose-additions when it already exists — your content is never edited without explicit approval.
- `.claude/settings.json` merges keys additively.

## License

MIT
