## Description

The pg-meta library currently supports listing, granting, and revoking privileges at the table level, but there is no equivalent support for column-level privileges. Many PostgreSQL deployments use column-level grants to control access at a finer granularity — for example, allowing a role to read only certain columns of a table — but there is currently no API to inspect or manage these privileges programmatically.

## Expected Behavior

- There should be a way to list all column privileges across the database, with results that include the schema, table name, column name, and the set of grantors, grantees, privilege types, and whether each grant is further grantable.
- The listing should support filtering down to specific columns of interest by their identifier.
- There should be a way to grant one or more column-level privileges (SELECT, INSERT, UPDATE, REFERENCES, or all of them at once) to a role on a specific column, identified by a column ID.
- There should be a way to revoke those column-level privileges from a role.
- All operations must work correctly for tables and columns whose names contain spaces or other characters that require special handling.

## Why This Matters

Without column privilege management, administrators and tooling built on pg-meta cannot inspect or control fine-grained data access at the column level. Adding this capability brings column-level privilege management to parity with existing table-level privilege support.
