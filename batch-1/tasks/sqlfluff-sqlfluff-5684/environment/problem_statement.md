## Description

SQLFluff's Snowflake dialect does not support external volume SQL statements. When users write Snowflake SQL that creates, modifies, drops, describes, or restores external volumes, SQLFluff fails to parse those files correctly. This causes linting pipelines to break or report spurious errors on valid Snowflake SQL.

## Expected Behavior

- Statements that create an external volume (with one or more storage locations specifying a cloud provider, base URL, optional encryption settings, and optional role/tenant identifiers) should parse successfully.
- Statements that alter an external volume — adding a storage location, removing a storage location, or setting properties like write access and comments — should parse successfully.
- Statements that drop an external volume, with or without an existence guard, should parse successfully as a distinct statement type.
- Statements that describe or restore (undrop) an external volume should parse successfully under the existing describe and undrop statement types.

## Why This Matters

Snowflake users who manage external data volumes as part of their data pipelines need to be able to lint those SQL files with SQLFluff without encountering parse failures. Supporting these statements brings the Snowflake dialect up to parity with the Snowflake documentation for external volume management commands.
