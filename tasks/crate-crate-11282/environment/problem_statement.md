## Description

The database catalog views (the low-level system views that expose metadata about tables, columns, functions, schemas, and constraints) do not currently respect the user privilege system. Any user — even one with no privileges at all — can query these catalog views and see metadata for every object in the database, including schemas, tables, functions, and columns they have never been granted access to.

This is a security and isolation issue: a restricted user should not be able to discover the existence of objects they are not permitted to access.

## Expected Behavior

- A user with no explicit privileges should only see catalog entries for objects they have been granted access to.
- Specifically: entries in the catalog views for tables, columns, constraints, functions, and schemas should be filtered based on the querying user's privileges.
- Granting a user access to a table (read privilege) should make the table's entries visible in the relevant catalog views.
- Granting a user access to a schema (read privilege) should make the schema and its functions visible in the relevant catalog views.
- The catalog schema itself, along with well-known internal system schemas, should always be freely accessible to all users — no explicit grant should be required to query catalog metadata about these built-in schemas.
- Creating tables or functions in a user-created schema should not expose those objects to unprivileged users through the catalog views.

## Why This Matters

Without this fix, the privilege system is incomplete: while an unprivileged user cannot directly read data from a restricted table, they can still discover that the table exists by querying catalog views. This breaks the expectation of schema-level isolation and can leak sensitive organizational information about the database structure.
