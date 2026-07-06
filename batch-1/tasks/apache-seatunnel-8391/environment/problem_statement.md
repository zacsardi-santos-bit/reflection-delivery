## Description

When reading change-data-capture events from a message broker using the debezium format, a single topic can carry events from multiple database tables simultaneously. Currently there is no way to configure the consumer to only process events from a specific table — all records on the topic are deserialized and forwarded downstream regardless of which database, schema, or table they originated from. This is a problem when a pipeline is only interested in changes from one particular table.

## Expected Behavior

- Users should be able to configure a table filter (specifying database name, schema name, and table name) when using debezium format. Only change events matching the specified table should be deserialized and forwarded; all other records should be silently dropped.
- The deserialization should gracefully handle cases where the incoming event does not include a column that is defined in the configured schema — those fields should produce null values instead of errors.
- For certain database connectors (such as Oracle) that may omit the database identifier from source metadata, the system should still be able to match records using only the schema and table name when no exact match is found with the database name.

## Related Fixes

- Avro serialization does not currently handle small integer field types or maps containing those types correctly. Serializing rows with these types should succeed and round-trip faithfully.

## Why This Matters

Without a table filter, consumers must process and discard unwanted records in downstream transformations. The missing filter support also prevents building targeted single-table pipelines on debezium-formatted multi-table topics. The deserialization robustness fix prevents unexpected failures when the source schema has more fields than the incoming event data.
