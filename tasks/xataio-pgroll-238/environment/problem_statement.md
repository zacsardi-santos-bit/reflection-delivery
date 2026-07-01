## Description

When adding a foreign key constraint to a column during a schema migration, two important column properties are silently dropped: the column's default value and any existing foreign key constraints from prior migrations. This makes it unsafe to incrementally evolve the schema of columns that already have defaults or prior foreign key relationships.

## Expected Behavior

- If a column has a default value and a foreign key constraint is added to it, the default value should still be applied when inserting rows without specifying that column — both while the migration is active and after it completes.
- If a column already has a foreign key constraint from a prior migration and a second foreign key constraint is being added, the original constraint should still be present on the table after the new migration completes.
- During the migration transition, the newly added foreign key constraint should be present on the temporary duplicate column under a temporary name, and renamed back to the original name upon completion.

## Why This Matters

Without these fixes, adding a foreign key to a column with a default causes inserts that omit the column to fail or produce incorrect results. Additionally, applying a sequence of foreign key migrations to the same column silently removes earlier constraints, leaving the schema in an inconsistent state that the database and application code no longer agree on.
