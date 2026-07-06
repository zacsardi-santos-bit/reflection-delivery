Implement the missing operations for SurrealDB integration in the Go framework to support inserting and deleting records, managing database schemas, and enabling migration functionalities. Ensure all operations handle connection errors gracefully.

*   Update the `Client` type in the `surrealdb` datasource package:
    *   Implement `CreateNamespace(ctx, namespace string) error` to send 'DEFINE NAMESPACE {name};'.
    *   Implement `DropNamespace(ctx, namespace string) error` to send 'REMOVE NAMESPACE {name};'.
    *   Implement `CreateDatabase(ctx, database string) error` to send 'DEFINE DATABASE {name};'.
    *   Implement `DropDatabase(ctx, database string) error` to send 'REMOVE DATABASE {name};'.
    *   Ensure all methods return `errNotConnected` if the database connection is nil.
    *   Add `executeQuery(ctx context.Context, operation, entity, query string) error` to handle query execution and connection errors.
    *   Modify `Insert` to return `errNotConnected` if not connected and `errUnexpectedResultType` if the result is not a `[]any` slice.
    *   Modify `Delete` to send 'DELETE FROM {table}:{id} RETURN BEFORE;' and handle empty result sets appropriately.
    *   Enhance `convertValue` to handle numeric conversions and return unchanged strings or unrecognized types.

*   Extend the `SurrealDB` interface in the `container` package:
    *   Add methods: `CreateNamespace(ctx context.Context, namespace string) error`, `CreateDatabase(ctx context.Context, database string) error`, `DropNamespace(ctx context.Context, namespace string) error`, and `DropDatabase(ctx context.Context, database string) error`.

*   Update the `Mocks` struct in the `container` package:
    *   Add a `SurrealDB` field of type `*MockSurrealDB`.
    *   Ensure `NewMockContainer` initializes `MockSurrealDB` and assigns it to the `SurrealDB` field.

*   Define `surrealDS` struct in the `migration` package:
    *   Include a `client` field of type `SurrealDB`.
    *   Implement methods: `Query(ctx, query, vars) ([]any, error)`, `CreateNamespace(ctx, namespace string) error`, `CreateDatabase(ctx, database string) error`, `DropNamespace(ctx, namespace string) error`, and `DropDatabase(ctx, database string) error` to delegate to the client.
    *   Implement `apply(m migrator) migrator` to wrap the SurrealDB datasource and the given migrator.

*   Update the `Datasource` struct in the `migration` package:
    *   Add a `SurrealDB` field of the `SurrealDB` interface type.

*   Define package-level constants in the `migration` package:
    *   `getLastSurrealDBGoFrMigration` with value 'SELECT version FROM gofr_migrations ORDER BY version DESC LIMIT 1;'.
    *   `insertSurrealDBGoFrMigrationRow` with value 'CREATE gofr_migrations SET version = $version, method = $method, start_time = $start_time, duration = $duration;'.

*   Implement SurrealDB migrator methods:
    *   `getLastMigration` to execute `getLastSurrealDBGoFrMigration`, extract 'version' as int64, and return 0 on error.
    *   `commitMigration` to call `Query` with `insertSurrealDBGoFrMigrationRow` and a map of bind variables.
    *   `beginTransaction` to log 'surrealDB migrator begin successfully'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.