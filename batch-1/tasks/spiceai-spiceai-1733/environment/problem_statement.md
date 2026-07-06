## Description

The Arrow SQL generation library builds DDL statements from Arrow schemas. When targeting PostgreSQL, it currently returns a single SQL string for table creation. This works for simple schemas, but breaks down when a schema contains structured (nested) fields.

PostgreSQL requires composite type definitions to exist in the database *before* a `CREATE TABLE` can reference them. With the current design that returns only one string, there is no way to include the prerequisite type-creation statements — they must be separate SQL statements executed in order.

## Expected Behavior

- The method that generates PostgreSQL table creation SQL should return a **sequence of SQL statements** rather than a single string.
- For schemas whose fields are all simple (non-structured) types, the sequence must contain exactly **one element** — the existing `CREATE TABLE IF NOT EXISTS` statement.
- For schemas that include structured fields, one composite type creation statement per structured field must be **prepended** to the sequence, followed by the `CREATE TABLE` statement.
- Each composite type creation statement should be **conditional** — it should only create the type if it does not already exist in the database, using PostgreSQL's procedural language block mechanism.
- The PostgreSQL feature must compile correctly, including its async test infrastructure, which is currently broken due to a missing dependency configuration.

## Why This Matters

Without this change, any schema containing nested structured fields cannot be materialized in PostgreSQL: the required composite type definitions are never emitted, causing the table-creation operation to fail. This fix is a prerequisite for full structural type support in the PostgreSQL backend.
