## Description

When using the Hudi Flink connector, users currently have to manually specify the path to an Avro schema file in their table configuration, even though the table definition (DDL) already contains the complete column schema. This is redundant and error-prone — anyone creating a Hudi table in Flink has to remember to also provide a separate schema file just to get reads and writes working.

The connector should be capable of automatically inferring the Avro deserialization schema from the table's column definitions. When no explicit schema file path or schema string is provided, the system should derive the correct Avro schema from the table schema automatically and use it for deserialization. When an explicit schema file path is provided, that should take precedence and the auto-inference should be skipped.

Additionally, the configuration key for specifying an explicit schema file path should be renamed to follow a clearer, more consistent naming convention across the connector's configuration options.

## Expected Behavior

- When creating a table source or sink without specifying a schema file path or schema string, the connector automatically infers the Avro schema from the DDL column definitions
- The inferred schema is stored internally in the configuration and used for deserialization
- When a schema file path is explicitly provided, the connector uses that path and does NOT infer/override the schema string
- The configuration key for the schema file path is renamed to follow a clearer, more consistent naming convention that groups related Avro schema options together under a common prefix
- A new configuration option accepts an Avro schema string directly (without needing a separate file)

## Why This Matters

This removes the need for users to maintain a separate Avro schema file and manually wire it into every Hudi table definition in Flink. It simplifies the setup, reduces boilerplate, and makes the connector easier to use for the common case where the DDL already describes the full table schema.
