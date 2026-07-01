## Description

When a new table is created in the database, the response incorrectly reports that 1 row was affected. Table creation is a DDL operation — it modifies the schema, not any data rows. The convention for such operations is to report 0 affected rows, since no actual row data is inserted, updated, or deleted. This incorrect count applies both to SQL-based table creation and to table creation via the gRPC interface.

## Expected Behavior

- Executing a CREATE TABLE statement via SQL should return 0 affected rows, not 1.
- Creating a table via the gRPC interface should also return 0 affected rows.

## Related Refactoring

As part of this fix, several internal API improvements are needed:

- The function that converts an alter expression into an alter table request should return the request directly rather than wrapping it in an optional. If the required field is missing, it should return an error. All callers must be updated accordingly.
- The function that converts a gRPC insert request into a table insert request should not require a separate schema reference; the column type information is already present in the insert request itself.
- The table alteration interface should support passing an opaque context map alongside the alter request, allowing callers to attach additional typed values that implementations can optionally consume.
- The distributed test setup should properly track temporary storage directories for the duration of tests, returning a unified structure holding both the frontend instance and the individual datanode instances.

## Why This Matters

Reporting 1 affected row for a DDL operation like CREATE TABLE is semantically incorrect and makes it harder for clients and tests to reliably interpret the outcome. Fixing this brings the behavior in line with standard database conventions.
