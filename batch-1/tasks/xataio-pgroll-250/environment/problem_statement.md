## Description

When altering a column that already has a unique constraint — whether to change its data type, add a check constraint, add a foreign key reference, or change its nullability — the unique constraint is silently dropped during the migration process. After the migration completes, data that should be rejected as a duplicate is accepted, breaking the uniqueness guarantee that was in place before the migration.

## Expected Behavior

- Changing a column's type should preserve any existing unique constraints on that column, both during the migration transition period and after the migration completes.
- Adding a check constraint to a column should preserve any existing unique constraints on that column, both during the transition period and after completion.
- Adding a foreign key constraint to a column should preserve any existing unique constraints on that column, both during the transition period and after completion.
- Setting a column to not null should preserve any existing unique constraints on that column, both during the transition period and after completion.

## Why This Matters

Data integrity depends on unique constraints continuing to be enforced regardless of what other alterations are made to a column. Silently dropping uniqueness during a migration can result in duplicate data entering the database, causing downstream application errors and data inconsistencies that are difficult to detect and repair.
