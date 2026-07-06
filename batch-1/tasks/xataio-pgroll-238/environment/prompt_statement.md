I'm working with a schema migration tool that supports adding foreign key constraints to existing columns as a non-breaking, dual-version migration. I've found two bugs with this operation.

First, if the column being constrained has a default value, that default is lost in the migrated view. Inserts through the new schema version that omit the column fail or produce wrong results, even though the column has a default defined. The default should be preserved and respected throughout the migration, including after it completes.

Second, if I run a migration to add a foreign key constraint to a column that already has a foreign key constraint from an earlier migration, the old constraint disappears after the new migration completes. It should still be present — applying a foreign key migration should preserve any prior foreign key constraints on that column, not remove them.

As part of the fix, I also need a test utility that can verify whether a named constraint exists on a specific database table, so these behaviors can be confirmed in tests.
