---
description: Security considerations when authoring slash commands, agents, or scaffolding logic
---

# Security

Bridle ships text files, but those files instruct Claude Code to perform actions on user systems. Authoring a command is authoring an automation surface.

## Filesystem
- Scaffolding commands write into the user's project directory only. Never instruct Claude to write outside the working directory or follow symlinks that escape it.
- `/generate-harness` and similar commands must check whether destination files already exist and ask before overwriting unless the user passed an explicit `--force` argument.

## Shell
- Commands that suggest shell snippets (e.g., `git`, `gh`) must use argv-style invocations, not interpolated strings. No concatenation of user input into shell commands.
- Don't suggest commands that bypass safety prompts (`rm -rf`, `git push --force`, `--no-verify`) unless the user explicitly asks.

## Secrets
- Never write secrets, tokens, or env values into templates. Use `{{ PLACEHOLDER }}` tokens for any value that varies by project.
- Hooks and MCP server configs must reference `${CLAUDE_PLUGIN_ROOT}` and read secrets from the user's environment, never from plugin files.

## Marketplace integrity
- The marketplace manifest must point at an exact ref (branch + sha) for releases. Avoid floating refs like `HEAD` for published versions.
- Every plugin file is read at plugin install time; treat the file content as user-trusted only after the user has reviewed it. Don't import or `eval` content from network sources at runtime.
