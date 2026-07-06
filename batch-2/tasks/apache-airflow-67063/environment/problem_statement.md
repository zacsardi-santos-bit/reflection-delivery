## Description

The CLI tool for managing Airflow connections has a bug where the database schema field of a connection is sent with the wrong name when making API requests. The connection data model uses an internal Python attribute name that differs from the expected API field name, and the serialization step was not applying the proper alias mapping. As a result, the schema field is transmitted incorrectly in requests to create, update, bulk-manage, and test connections.

Additionally, when importing connections from a JSON file, if the file includes a schema value for a connection, that value is silently dropped — it is never forwarded to the API.

## Expected Behavior

- When creating, updating, bulk-operating on, or testing a connection, the outgoing HTTP request body should use the API-facing field name for the schema — not the internal Python attribute name — so that the server correctly receives the schema value.
- When importing connections from a JSON file that includes a schema value, that value should be preserved and sent to the API correctly.

## Why This Matters

Users who rely on the schema field of a connection (common in database connections like PostgreSQL, Snowflake, etc.) are silently losing this configuration when using the CLI. The server never receives the schema value, so the connection ends up missing critical configuration. This also affects bulk import workflows where exporting then re-importing connections causes the schema to be dropped.
