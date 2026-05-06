# Dependencies

Every dependency is code your team didn't write executing inside your process. Trust is a function of pinning, audit cadence, and review.

## IRON LAWS

**NO UNPINNED DEPENDENCIES IN MANIFESTS.**

Floating ranges (`^1.2.3`, `~1.2.3`, `*`, `latest`) in committed manifests are forbidden. The lockfile pins versions; the manifest must agree. A floating range that resolves differently on two machines is the next supply-chain incident.

**NO DEPENDENCY ADDED WITHOUT JUSTIFICATION IN THE COMMIT.**

A new dependency is a permanent obligation: updates, security patches, license drift, replacement when abandoned. The commit that adds it must say *why this dep, why not write it, why not vendor*. "Convenience" is a real reason — but state it.

## GOLDEN RULES

- **Aim for the smallest dependency that solves the problem.** Pulling in a 200-package tree to avoid 20 lines of code is rarely the right trade.
- **Aim for direct dependencies you can name.** Transitive dependencies are someone else's choice; you inherit them anyway. The fewer direct ones, the smaller your tree.
- **Aim for license alignment.** Permissive (MIT, Apache-2.0, BSD) by default. GPL/AGPL/SSPL only with deliberate decision and an ADR.

## Adding a dependency

Before `npm install` / `pip install` / `bundle add` / `cargo add`:

1. Is there an existing dependency in the tree that solves this? Re-use over add.
2. Could 50 lines of code in this codebase replace it? Vendor over add.
3. Is the package maintained? Last release, open issue count, maintainer signals.
4. Is the license compatible? Check `LICENSE` in the package.
5. What does the dependency tree look like? `npm ls`, `pip-tree`, `cargo tree`. A dep that adds 50 transitive packages is 50 packages of trust.

## Updating dependencies

- **Cadence over crisis.** Run `{{ DEPENDENCY_AUDIT_COMMAND }}` regularly, not only when a CVE hits the news.
- **Lockfiles are committed.** Never `--no-lockfile`. Never `.gitignore` the lock.
- **Major bumps deserve an ADR-style note.** Patch and minor bumps land in normal commits. Major bumps may break behavior — capture the why.

## Removing dependencies

- **Removing a dep is a feature.** Smaller surface, fewer updates, fewer audits. Treat removal as net-positive work, not as cleanup.

## Supply chain

- **Lockfile drift is a smell.** Two PRs touching the lockfile and only one touching the manifest deserves a second look.
- **Postinstall scripts are arbitrary code execution.** Audit them, or set the package manager to ignore postinstall by default.
- **Mirror what you depend on, where the project policy requires it.** Public registries are not infrastructure you control.

## Project-Specific Notes

{{ DEPENDENCY_NOTES }}
