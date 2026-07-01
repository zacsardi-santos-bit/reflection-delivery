## Description

When building data pipelines that read change-data-capture (CDC) events from multiple sources simultaneously — for example, multiple topics using different serialization formats — each row that gets deserialized needs to carry information about which table it came from. Currently, the deserialization schemas only accept raw schema type information (field names and types) and have no mechanism to associate each row with a source table identity.

This gap causes rows deserialized from different sources to lose their table context, making it impossible to distinguish or properly route rows by origin when multiple sources feed into a single sink. It also prevents multi-source pipeline configurations from functioning correctly.

## Expected Behavior

- Deserialization schemas for JSON formats (including CDC-specific formats), Avro, and related schemas should accept richer table metadata objects that encapsulate both the schema structure and the table's identity (catalog, database, table name).
- After deserialization, each row should carry the table identifier derived from the table metadata object, so downstream components can identify the row's origin.
- A utility method should be available to construct these table metadata objects from an existing schema type, using provided catalog, database, and table name strings.
- Multi-source Kafka pipelines reading from different topics using different formats (e.g., two different CDC formats) should be able to correctly merge and sink data to a single destination.

## Why This Matters

Without this capability, developers cannot build multi-source CDC pipelines where rows need to be tracked back to their origin table. This is essential for scenarios like reading from multiple Kafka topics (each representing a different upstream table) and merging the results into a single target table with correct upsert semantics.
