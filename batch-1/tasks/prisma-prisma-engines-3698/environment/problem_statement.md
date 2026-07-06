## Description

The table data structure in the SQL schema describer library currently represents table properties with a single boolean field — the partition indicator — to track whether a table is a partition table. This approach doesn't scale: as we add support for detecting more table characteristics (such as whether a table has inheriting child tables via PostgreSQL's table inheritance feature), we need additional booleans for each new property.

We should replace this single boolean with a proper bit-flag field that can hold multiple independent boolean properties at once. This makes the data model more extensible and avoids proliferating boolean fields in the struct.

## Expected Behavior

- The table data structure should have a new bitflags-typed field replacing the existing boolean partition field.
- A new enum should define the available table property flags, including at least one for partition tables and one for tables that have subclass tables (child tables that inherit from the parent using PostgreSQL-style table inheritance).
- The serialized JSON output for tables should reflect this change: where the old format produced a boolean value for the partition property, the new format should produce a numeric integer encoding the combined flags (zero when no flags are set).
- The walker methods on tables should be updated: the existing partition-check method must read from the new flags field, and a new method should be added to check whether a table has subclass tables.
- All database-specific describers (MySQL, PostgreSQL, MSSQL, SQLite) must be updated to use the new API for creating tables with specific properties.

## Why This Matters

By using a bit-flag approach, we can represent multiple table properties compactly and avoid a proliferation of boolean fields as new table characteristics are discovered. This also unblocks better support for PostgreSQL table inheritance, which requires knowing whether a table has child tables.
