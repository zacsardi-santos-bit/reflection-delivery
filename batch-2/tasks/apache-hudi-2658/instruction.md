Implement automatic Avro schema inference in the Hudi Flink connector to eliminate the need for users to specify a schema file path manually. Ensure the connector derives the Avro schema from the table's column definitions unless an explicit schema file path or schema string is provided.

*   Update the `FlinkOptions` class:
    *   Define a config option `READ_AVRO_SCHEMA_PATH` with key `read.avro-schema.path`, string type, no default value, replacing `READ_SCHEMA_FILE_PATH`.
    *   Define a config option `READ_AVRO_SCHEMA` with key `read.avro-schema`, string type, no default value, to hold an Avro schema string directly.

*   Modify `HoodieTableFactory`:
    *   In `createTableSource()`:
        *   Infer the Avro schema from the table's physical row type using `AvroSchemaConverter.convertToSchema()` and set it as `READ_AVRO_SCHEMA` in the configuration if neither `READ_AVRO_SCHEMA_PATH` nor `READ_AVRO_SCHEMA` is present.
        *   Do not set `READ_AVRO_SCHEMA` if `READ_AVRO_SCHEMA_PATH` is already set; leave it absent (null).
    *   In `createTableSink()`, apply the same schema inference behavior as `createTableSource()`.

*   Update `HoodieTableSource` and `HoodieTableSink`:
    *   Implement a public `getConf()` method returning the internal `Configuration` object to allow inspection of set config options, including inferred schema values.

*   Ensure the inferred `READ_AVRO_SCHEMA` for a table with fields (uuid: nullable string, name: nullable string, age: nullable int, ts: nullable timestamp-millis, partition: nullable string) matches the Avro JSON record:
    ```
    {"type":"record","name":"record","fields":[{"name":"uuid","type":["null","string"],"default":null},{"name":"name","type":["null","string"],"default":null},{"name":"age","type":["null","int"],"default":null},{"name":"ts","type":["null",{"type":"long","logicalType":"timestamp-millis"}],"default":null},{"name":"partition","type":["null","string"],"default":null}]}
    ```

*   Ensure existing integration tests for Hudi tables without explicit schema file paths succeed, with schemas derived automatically from DDL column definitions.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.