## Description

The BigQuery IO manager currently uses a single fixed strategy for clearing data before writing: it always drops the entire destination table. This one-size-fits-all approach is too rigid for real-world use cases. Users who want to preserve the table's schema and metadata while only removing its rows (truncate), or who want to add data to an existing table without any pre-write deletion (append), have no way to configure this behavior.

## Expected Behavior

- Users should be able to configure a write mode for the BigQuery IO manager that controls how data is written to the destination table.
- In truncate mode, only the table's data should be cleared — the table itself (along with its schema and settings) should be preserved.
- In replace mode, the existing table should be fully dropped and recreated during the write.
- In append mode, no pre-write cleanup should occur — new data is simply added to the existing table.
- For partitioned tables, the existing per-partition deletion logic should continue to work as before, regardless of the configured write mode.
- The write mode setting and any GCP credentials configured on the IO manager should be correctly passed through to the underlying client that executes queries.

## Why This Matters

Without this flexibility, users are forced to accept full table replacement even when they only want to clear rows or append new data. Truncate mode is valuable when downstream tools depend on BigQuery table metadata that would be lost on a full drop. Append mode is essential for incremental loading scenarios. Additionally, credentials must flow correctly to the client to avoid authentication failures in tools that require non-null keyfile credentials.
