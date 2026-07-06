Simplify the connection management for the SQLite vector store connector by allowing it to accept connection strings directly. Implement changes to constructors and methods to facilitate this, ensuring the library manages the connection lifecycle internally.

*   Update `SqliteVectorStoreCollectionCommandBuilder`:
    *   Convert to a static class.
    *   Ensure all methods are static and accept a `SqliteConnection` as the first parameter.
    *   Implement `BuildTableCountCommand` to accept a `SqliteConnection` and a table name, returning a `DbCommand` with CommandText: "SELECT count(*) FROM sqlite_master WHERE type='table' AND name=@tableName;".
    *   Implement `BuildCreateTableCommand` to accept a `SqliteConnection`, table name, column definitions, and an `ifNotExists` flag, returning a `DbCommand` containing "CREATE TABLE".
    *   Implement `BuildCreateVirtualTableCommand` to accept a `SqliteConnection`, table name, column definitions, an `ifNotExists` flag, and an extension name, returning a `DbCommand` containing "CREATE VIRTUAL TABLE".
    *   Implement `BuildDropTableCommand` to accept a `SqliteConnection` and a table name, returning a `DbCommand` with CommandText: "DROP TABLE [<tableName>];".
    *   Implement `BuildSelectCommand` to accept a `SqliteConnection`, a table name, a list of column names, a list of where conditions, and an optional `orderBy` property name, returning a `DbCommand` containing a SELECT statement.
    *   Implement `BuildSelectLeftJoinCommand` to accept a `SqliteConnection`, left and right table names, join column, property name lists, conditions, and optional ordering/filter parameters, returning a `DbCommand` with a LEFT JOIN SELECT statement.
    *   Implement `BuildDeleteCommand` to accept a `SqliteConnection`, a table name, and a list of where conditions, returning a `DbCommand` containing "DELETE FROM [<tableName>]".
    *   Implement `BuildInsertCommand` to accept a `SqliteConnection`, table name, row identifier, column names, row data, and an optional `replaceIfExists` flag, returning a `DbCommand` for an INSERT operation.

*   Update `SqliteVectorStore`:
    *   Provide a constructor that accepts a connection string (`string`) as its first parameter and an optional `SqliteVectorStoreOptions`.
    *   Deprecate the constructor that accepts a `DbConnection` as the primary constructor.

*   Update `SqliteVectorStoreRecordCollection`:
    *   Provide a constructor that accepts a connection string (`string`) as its first parameter, a collection name, and optional `SqliteVectorStoreRecordCollectionOptions`.
    *   Deprecate the constructor that accepts a `DbConnection` as the primary constructor.

*   Update `SqliteServiceCollectionExtensions`:
    *   Modify `AddSqliteVectorStore` to accept a connection string as a required parameter and register a `SqliteVectorStore` as `IVectorStore` in the DI container.
    *   Modify `AddSqliteVectorStoreRecordCollection<TKey, TRecord>` to accept a collection name and a connection string, registering a `SqliteVectorStoreRecordCollection` as both `IVectorStoreRecordCollection<TKey, TRecord>` and `IVectorizedSearch<TRecord>` in the DI container.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.