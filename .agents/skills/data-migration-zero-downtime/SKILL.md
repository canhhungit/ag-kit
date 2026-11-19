---
name: data-migration-zero-downtime
description: Zero-downtime database migration patterns covering expand-and-contract schema changes, online index creation, batched backfills, and safe rollbacks.
when_to_use: "When altering production database schemas, renaming/dropping columns, adding indexes on large tables, or writing data backfill scripts."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Data Migration — Zero-Downtime Schema Evolution

> Old and new application code must both work cleanly during deployment — never break running replicas with a destructive migration.

## Expand-and-Contract Protocol

1. **Expand (Backward-Compatible)**:
   - Add new nullable columns, new tables, or optional fields without removing or renaming existing ones.
   - Deploy code that dual-writes (writes both old and new fields) while still reading from the old field (or falling back cleanly).
2. **Backfill in Batches**:
   - Migrate historical rows in bounded batches (e.g., 500–2,000 rows per batch with a short pause) so you do not saturate IOPS, replication lag, or connection pools.
   - Make backfill scripts **idempotent** and resumable.
3. **Switch Reads & Enforce Constraints**:
   - Deploy code reading from the new column/field.
   - Add `NOT NULL` / foreign keys / unique constraints only after backfill verification.
4. **Contract (Cleanup)**:
   - Remove dual-writes and drop obsolete columns/indexes in a later release after confirming zero rollback risk.

## Index & Lock Safety

- **PostgreSQL**: Use `CREATE INDEX CONCURRENTLY` (outside a transaction block) on large tables to avoid blocking writes.
- **MongoDB**: Ensure compound indexes match ESR order (Equality → Sort → Range) and verify index builds do not degrade primary latency.
- Always test migrations against production-like data volume and have a verified rollback/restore plan.
