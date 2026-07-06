## Description

The SurrealDB integration is incomplete. While basic querying of existing records works, several fundamental operations are missing: there is no way to insert new records into a table, delete records by their identifier, or manage the database schema by creating or removing namespaces and databases. This makes it impossible to build a fully functional application on top of the integration.

Additionally, the framework's migration system — which is used to track and run versioned schema changes in a structured way — does not support SurrealDB at all. Developers who want to manage their SurrealDB schema through the framework's migration tooling have no path to do so.

## Expected Behavior

- Developers should be able to insert new records into a named table and receive the inserted records back.
- Developers should be able to delete a record by table name and identifier, receiving the deleted record if it existed or a clear empty result if not.
- Developers should be able to create and drop namespaces and databases through the client.
- All operations should return an appropriate "not connected" error if the database connection has not been established.
- SurrealDB should be supported by the migration system, allowing developers to run versioned schema changes with proper tracking of migration version, method, start time, and duration.
- The migration lifecycle (checking/creating the migration tracking table, retrieving the last migration, committing a migration, and beginning a transaction) should all function correctly for SurrealDB.

## Why This Matters

Without these operations, SurrealDB is only usable for read workloads and cannot be used for typical application development patterns that require writes, deletes, schema management, or structured database evolution via migrations.
