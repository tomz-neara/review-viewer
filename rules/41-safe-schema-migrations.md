---
summary: "Checks that schema and data migrations preserve backward compatibility and are tested"
file_patterns: [["*.sql"], ["*.dart"], ["*.java"], ["*.py"]]
tags: ["mikeh", "correctness", "data-safety", "migrations"]
sources: ["D74756", "D72056", "D68441", "D72813", "D87663"]
---
# Safe Schema Migrations

If this rule is not relevant to the code under review, state so and move on.

Schema changes and data migrations are among the riskiest operations in a codebase. Mike carefully checks that migrations do not break existing clients, lose data, or introduce subtle corruption. A column rename, type change, or new NOT NULL constraint can silently break readers that haven't been updated yet. This complements rules 25 (schema nullability) and 28 (schema naming) which cover schema correctness; this rule covers migration safety.

- Flag adding a NOT NULL column without a default value — existing rows will fail the constraint.
- Flag removing or renaming a column/field that existing code or clients may still read — ensure all readers are updated first, or keep the old field populated during a transition period.
- Flag data migrations that lack a rollback plan or reversibility explanation.
- Flag schema changes deployed in the same release as the code that reads the new schema — if the migration fails, the code breaks. Prefer: migrate first, deploy readers second.
- Flag migrations that transform data in-place without a backup or verification step for large tables.
- Additive changes (new nullable columns, new tables) are generally safe and don't need special handling.

## Example

Bad:
```sql
-- Renames column with no transition period — existing code reading 'email' breaks immediately
ALTER TABLE users RENAME COLUMN email TO contact_email;
```

Good:
```sql
-- Step 1: Add new column, backfill from old (this migration)
ALTER TABLE users ADD COLUMN contact_email VARCHAR(255);
UPDATE users SET contact_email = email WHERE contact_email IS NULL;

-- Step 2: Deploy code that reads contact_email (separate release)
-- Step 3: Drop old column after all readers migrated (future migration)
```
