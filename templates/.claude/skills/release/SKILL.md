---
name: release
description: Cut a release — propose a semver bump, update the changelog, bump the version file, tag, push. Conventional release commits.
argument-hint: "[version, e.g. 1.4.0 — optional, will propose if omitted]"
---

Cut a release of **{{ PROJECT_NAME }}**: $ARGUMENTS

If `$ARGUMENTS` is empty, propose a version based on the unreleased commits and ask for confirmation.

This skill honors `.claude/rules/bridle-mode.md`. In **paired**, confirm at each step. In **solo**, run end-to-end and stop only if the bump is ambiguous or verification fails. In **autopilot**, decide and proceed; surface the bump rationale at final review.

## IRON LAWS

**NO VERSION BUMP WITHOUT A CHANGELOG ENTRY.**

The version file and the changelog change in the same commit. A release commit that bumps the version without describing what's in it is a release no one can audit.

**NO RELEASE FROM A DIRTY OR UNVERIFIED TREE.**

`git status` clean. `{{ TEST_COMMAND }}` and `{{ LINT_COMMAND }}` green in this turn. No exceptions for "small" releases — small releases that ship broken builds are how trust dies. See `.claude/rules/verification-before-completion.md`.

## GOLDEN RULES

- **Aim for semver discipline.** Breaking → MAJOR. Backward-compatible feature → MINOR. Backward-compatible fix → PATCH. Pre-1.0, breaking can land in MINOR — call it out.
- **Aim for changelogs that read in five years.** A future reader has no context. "Fixed bug" is useless; "Fixed off-by-one in pagination cursor when result count equals page size" is forever-readable.
- **Aim for a release commit that does only the release.** No drive-by fixes, no doc tweaks, no last-minute additions. The release commit is a label.

## Workflow

### Phase 1: Verify clean state
1. `git status` — must be clean.
2. `git fetch && git status` — must be up to date with `origin/{{ MAIN_BRANCH }}`.
3. Run `{{ LINT_COMMAND }}` and `{{ TEST_COMMAND }}`. Both must pass in this turn.
4. If any of the above fail, stop. Do not propose to "release first, fix later."

### Phase 2: Propose the version
5. List commits since the last tag: `git log <last-tag>..HEAD --oneline`.
6. Classify the changes against semver:
   - Any breaking change → MAJOR
   - Any new feature, no breaking → MINOR
   - Only fixes / docs / chores → PATCH
7. Present the proposed version with rationale. Ask for confirmation.

### Phase 3: Update the version file
8. Bump the version in the project's version file (e.g., `package.json`, `pyproject.toml`, `Cargo.toml`, `plugin.json`, `mix.exs`). Detect from the project; ask if ambiguous.

### Phase 4: Update CHANGELOG.md
9. Read the existing `CHANGELOG.md` to match its format. Most projects follow [Keep a Changelog](https://keepachangelog.com/).
10. Add a new section at the top under the title:
    ```
    ## [<version>] — YYYY-MM-DD

    ### Added
    - …

    ### Changed
    - …

    ### Fixed
    - …
    ```
11. Translate commits into changelog entries. Audience is humans, not git. Group by Added / Changed / Fixed / Deprecated / Removed / Security as applicable.
12. Show the proposed changelog section. Ask for feedback.

### Phase 5: Commit & tag
13. Commit as `chore(release): <version>` with both the version file and CHANGELOG staged together.
14. Tag the commit: `git tag v<version> <sha>` (lightweight tag matches most projects' convention; use annotated `git tag -a` if the project does).
15. Push branch and tag: `git push origin {{ MAIN_BRANCH }} && git push origin v<version>`.

### Phase 6: Post-release
16. Confirm the tag is visible: `git ls-remote --tags origin | grep v<version>`.
17. If the project publishes (npm, PyPI, container registry, marketplace), suggest the publish command — but do not run it without explicit user confirmation.

## Key Rules

- Pre-1.0 versions can ship breaking changes in MINOR — but call them out under `### Changed` with a "**Breaking:**" prefix.
- Yanked versions: never delete a tag once pushed. Mark the version `[YANKED]` in the changelog and release a follow-up.
- One release per commit. Cherry-picking a release commit anywhere else is forbidden.
