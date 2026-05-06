# Database Migrations

Migrations are deployed code. Once they run in production, they cannot be unrun cleanly. Treat each one as forward-only and zero-downtime.

## IRON LAWS

**NO DESTRUCTIVE CHANGE IN THE SAME RELEASE AS CODE THAT DEPENDS ON THE NEW SHAPE.**

Drop column, drop table, narrow a type, rename a column, change a default, add NOT NULL without backfill — never paired with code that requires the change. Use **expand → migrate → contract**:

1. **Expand:** add the new column / table / index. Code reads from old AND writes to both.
2. **Migrate:** backfill data. Verify.
3. **Contract:** stop writing to the old column. Stop reading from it. Drop it — in a *later* release, after the previous one has been deployed everywhere.

A migration that breaks rolling deployments is a migration that breaks production.

**NO MIGRATION WITHOUT A REHEARSAL.**

Run the migration against a copy of production-shaped data before merging. "It worked in test" with 100 rows tells you nothing about 50M.

## GOLDEN RULES

- **Aim for narrow, additive migrations.** One concern per file. A migration that does five things will fail in the middle.
- **Aim for index creation that won't lock.** Postgres `CREATE INDEX CONCURRENTLY`. MySQL online DDL. Locked migrations on large tables block writes.
- **Aim for migrations that are fast to run, slow to roll back.** A 10-second migration is easy to deploy. A 4-hour migration needs to be backgrounded — and definitely cannot run in a deploy hook.

## Process

1. **Write forward and reverse.** A migration without a `down` (or with `down = no-op`) is acceptable for additive changes — but state it explicitly.
2. **Backfill is its own step.** Don't backfill in the same migration as the schema change. Schema change runs in seconds; backfill may run in hours.
3. **Test against production-shaped data.** Row counts, index sizes, constraint volumes. Migrations that work on dev datasets routinely fail on prod.
4. **Wrap multi-step migrations in transactions where possible** — but be aware that some DDL cannot run inside a transaction.

## Review checklist

- [ ] Forward-compatible: does old code work after the migration runs?
- [ ] Backward-compatible: does the migration work if old code is still running during deploy?
- [ ] Locking: does this take a long-held lock on a hot table?
- [ ] Backfill: is the data backfill separate, idempotent, and resumable?
- [ ] Rollback: if the next deploy is reverted, does this migration leave the system in a working state?

## Project-Specific Notes

{{ MIGRATION_NOTES }}
