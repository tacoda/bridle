---
description: How to keep a session's context clean — when to clear, when to compact, when to split into worktrees
---

# Session Hygiene

Long sessions drift. Context fills with stale exploration, abandoned attempts, and corrections that no longer apply. The agent gets less coherent, not more, the longer a session runs.

## Reset triggers

- **Corrected twice on the same thing** → `/clear` and re-prompt with the lesson built in. A third correction means the context is fighting you.
- **Direction has changed** (different feature, different file, different intent) → `/clear`. Bringing yesterday's exploration into today's task is a slow leak.
- **Session past ~45 minutes of active work** → `/compact`. The early turns are likely dead weight.

## Worktrees for parallel work

Run independent tasks in separate `git worktree`s with separate Claude sessions. Do not interleave two features in one session — the cross-talk costs more than the worktree setup.

Three concurrent worktrees is a soft ceiling. Beyond that, attention divides faster than parallelism saves time.

## What survives across sessions

- Rules in `.claude/rules/` — reload on every session.
- `CLAUDE.md`, `HARNESS.md`, `GLOSSARY.md` — same.
- Feature docs in `.claude/features/` — read on demand.

What does **not** survive: per-conversation discoveries. If a discovery is worth keeping, write it into a rule, a feature doc, or `GLOSSARY.md` *before* clearing.

## Anti-patterns

- Treating `/clear` as failure. It's a tool — use it.
- Asking the agent to "remember" something across sessions without writing it down.
- One mega-session that touches five features. Split it.
