Refactor the Postgres SQL generation helper class into a static class with static methods, eliminating the need for instantiation and removing the associated interface. Update all internal references to use these static methods directly.

*   Convert the `PostgresVectorStoreCollectionSqlBuilder` class into a static class named `PostgresSqlBuilder` located at `dotnet/src/Connectors/Connectors.Memory.Postgres/PostgresSqlBuilder.cs`.
*   Remove the `IPostgresVectorStoreCollectionSqlBuilder` interface.
*   Implement the following static methods in `PostgresSqlBuilder`:
    *   `static BuildCreateTableCommand(string schema, string collectionName, VectorStoreRecordModel model, bool ifNotExists) -> NpgsqlCommand`
        *   Ensure the command text includes the schema-qualified table name in the form `schema."collectionName"`.
    *   `static BuildCreateIndexCommand(string schema, string collectionName, string vectorColumn, string indexKind, string distanceFunction, bool isVector, bool ifNotExists) -> NpgsqlCommand`
        *   Throw `NotSupportedException` for non-HNSW index kinds when `isVector` is true.
        *   Generate non-vector index SQL with or without `IF NOT EXISTS` based on the `ifNotExists` flag.
    *   `static BuildDropTableCommand(string schema, string collectionName) -> NpgsqlCommand`
        *   Ensure the command text includes `DROP TABLE IF EXISTS schema."collectionName"`.
    *   `static BuildUpsertCommand(string schema, string collectionName, string keyColumn, Dictionary<string, object?> row) -> NpgsqlCommand`
        *   Ensure the command text includes `INSERT INTO schema."collectionName"`.
    *   `static BuildUpsertBatchCommand(string schema, string collectionName, string keyColumn, IList<Dictionary<string, object?>> rows) -> NpgsqlCommand`
        *   Ensure the command text includes `INSERT INTO schema."collectionName"`.
    *   `static BuildGetCommand(string schema, string collectionName, VectorStoreRecordModel model, object key, bool includeVectors) -> NpgsqlCommand`
        *   Ensure the command text includes `SELECT`.
    *   `static BuildGetBatchCommand(string schema, string collectionName, VectorStoreRecordModel model, IEnumerable<object> keys, bool includeVectors) -> NpgsqlCommand`
        *   Ensure the command text includes `SELECT`.
    *   `static BuildDeleteCommand(string schema, string collectionName, string keyColumn, object key) -> NpgsqlCommand`
        *   Ensure the command text includes `DELETE`.
    *   `static BuildDeleteBatchCommand(string schema, string collectionName, string keyColumn, IEnumerable<object> keys) -> NpgsqlCommand`
        *   Ensure the command text includes `DELETE`.
*   Update all internal references in `PostgresVectorStoreRecordCollection` and other Postgres connector internals to call `PostgresSqlBuilder` static methods directly.
*   Ensure all existing Postgres connector unit tests continue to compile and pass without modification.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.