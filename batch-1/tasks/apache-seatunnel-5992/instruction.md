Update the deserialization schemas to accept a richer table metadata object that includes both schema structure and table identity. Implement utility methods to construct these metadata objects and ensure that deserialized rows carry their source table identifiers.

*   Modify the `JsonDeserializationSchema` class:
    *   Change the constructor to accept `CatalogTable` as the first parameter, followed by `boolean failOnMissingField` and `boolean ignoreParseErrors`.
    *   Throw `SeaTunnelJsonFormatException` when constructed with a null `CatalogTable` and both `failOnMissingField=true` and `ignoreParseErrors=true`.

*   Update the `AvroDeserializationSchema` class:
    *   Change the constructor to accept `CatalogTable` instead of `SeaTunnelRowType`.

*   Modify the `CanalJsonDeserializationSchema` class:
    *   Update the `Builder` constructor and the static `builder()` method to accept `CatalogTable`.
    *   Ensure the `tableId` on each deserialized `SeaTunnelRow` is set to the `CatalogTable`'s table path.

*   Update the `DebeziumJsonDeserializationSchema` class:
    *   Change the two-argument and three-argument constructors to accept `CatalogTable` as the first parameter.
    *   Ensure the `tableId` on deserialized rows is set to ".." when using a `CatalogTable` created via `CatalogTableUtil.getCatalogTable("", "", "", "", rowType)`.

*   Modify the `OggJsonDeserializationSchema` class:
    *   Update the `Builder` constructor and the static `builder()` method to accept `CatalogTable`.
    *   Ensure the `tableId` on each deserialized `SeaTunnelRow` is set to the `CatalogTable`'s table path.

*   Implement the `CatalogTableUtil` class:
    *   Add the `getCatalogTable` method to accept `String catalogName`, `String database`, `String tableName`, `String tableComment`, and `SeaTunnelRowType`.
    *   Return a `CatalogTable` that encapsulates the provided `SeaTunnelRowType` and table path from the first three string parameters.

*   Ensure a Kafka source reading from multiple topics with different formats can write merged results to a single JDBC sink, with the combined row count matching the union of all source events after CDC operations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.