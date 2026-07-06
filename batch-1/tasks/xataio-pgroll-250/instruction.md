Implement a solution to ensure that unique constraints on database columns are preserved during and after column-alteration migrations. Ensure that any modifications to a column do not result in the loss of its unique constraint, thus maintaining data integrity.

*   Preserve UNIQUE constraints during column-alteration migrations:
    *   Ensure that changing a column's data type retains the UNIQUE constraint.
    *   Ensure that adding a CHECK constraint to a column retains the UNIQUE constraint.
    *   Ensure that adding a FOREIGN KEY constraint to a column retains the UNIQUE constraint.
    *   Ensure that setting a column to NOT NULL retains the UNIQUE constraint.

*   Enforce UNIQUE constraints during and after migrations:
    *   Reject duplicate values with a unique-violation error during and after the migration.
    *   Allow non-duplicate values to be inserted successfully during and after the migration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.