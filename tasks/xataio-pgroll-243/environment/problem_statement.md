## Description

When tests verify that inserting a row into the database is correctly blocked by a constraint, they currently only confirm that the insert was rejected — not that it was rejected for the right reason. A test checking that a foreign key constraint is enforced, for example, would pass even if the insert failed due to an entirely different cause (such as a schema mismatch or a type error in the test data).

## Expected Behavior

- When an insert is expected to fail due to a specific type of constraint violation (such as a referential integrity rule, a check condition, a not-null requirement, or a uniqueness requirement), the test infrastructure should be able to verify the exact category of the database error.
- Shared error category identifiers should be available as named constants so individual tests can declare which violation type they expect, making the test intent explicit and self-documenting.
- Constants should cover all commonly tested constraint violation types: foreign key violations, check constraint violations, NOT NULL violations, and unique constraint violations.

## Why This Matters

Without this, a flawed migration or a test data mistake could silently mask the actual database behavior. Constraint tests become much more trustworthy when they confirm not just that an insert was blocked, but that it was blocked by the intended rule.
