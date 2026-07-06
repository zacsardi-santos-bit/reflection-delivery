Implement a general-purpose helper for cursor-based pagination in the SQL database layer. Update existing batch query configurations to support both batch and paginated queries under a unified configuration type. Ensure that all related functions and structures reflect these changes.

*   Replace the `PagedQueryConfig` struct with a new `QueryConfig` struct in the `sqldb` package.
    *   `QueryConfig` must include:
        *   `MaxBatchSize` (type `int`) for batch/IN-clause queries.
        *   `MaxPageSize` (type `int32`) for cursor-based paginated queries.
*   Replace the `DefaultPagedQueryConfig()` function with `DefaultQueryConfig()`.
    *   `DefaultQueryConfig()` must return a `*QueryConfig` with sensible defaults for both `MaxBatchSize` and `MaxPageSize`.
*   Rename the `ExecutePagedQuery` function to `ExecuteBatchQuery` in `sqldb/paginate.go`.
    *   Update the function to accept `*QueryConfig` and use `cfg.MaxBatchSize` for chunk size.
    *   Ensure it returns `nil` for empty `inputItems`.
    *   Propagate query errors with "query failed for page" and callback errors with "callback failed for result:".
*   Add a new function `ExecutePaginatedQuery` in the `sqldb` package.
    *   Accept the following parameters: a context, a `*QueryConfig`, an `initialCursor` of type `C`, a `queryFunc`, an `extractCursor`, and a `processItem`.
    *   Call `queryFunc(ctx, cursor, cfg.MaxPageSize)` on each iteration.
    *   Stop iteration when `queryFunc` returns an empty slice or fewer items than `cfg.MaxPageSize`.
    *   Update the cursor using `extractCursor` after processing each item.
    *   Return a wrapped error for `queryFunc` errors with "failed to fetch page with cursor %v" and for `processItem` errors with "failed to process item".
    *   Ensure errors are unwrappable via `errors.Is`.
    *   Support a non-zero `initialCursor` to fetch and process items beyond that cursor.
*   Update the `SQLStoreConfig` struct in `graph/db/sql_store.go`.
    *   Rename the `PaginationCfg` field to `QueryCfg` of type `*sqldb.QueryConfig`.
    *   Ensure the `NewSQLStore` constructor accepts this renamed field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.