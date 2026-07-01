Implement a job listing capability in the job queue library by creating a low-level database function to query jobs with flexible filtering options. Update the database adapter interface to expose methods for executing these queries both directly and within transactions.

*   Create a new package `internal/dblist` with a `JobList` function:
    *   Execute a parameterized SQL SELECT against the `river_job` table.
    *   Accept parameters: context, `pgx.Tx` transaction, and `JobListParams` struct.
    *   Return a slice of `*dbsqlc.RiverJob` pointers and an error.

*   Define `JobListParams` in `internal/dblist` with fields:
    *   `State dbsqlc.JobState` (optional).
    *   `LimitCount int32` (required, > 0).
    *   `OrderBy []JobListOrderBy` (required, non-empty).
    *   `Conditions string` (optional).
    *   `NamedArgs map[string]any`.
    *   `Priorities []int16`.

*   Define `JobListOrderBy` in `internal/dblist` with fields:
    *   `Expr string`.
    *   `Order SortOrder`.

*   Define `SortOrder` as an integer type with constants:
    *   `SortOrderUnspecified`, `SortOrderAsc`, `SortOrderDesc`.

*   Implement `JobList` to:
    *   Build a dynamic SQL query with optional `state` and `Conditions`.
    *   Construct ORDER BY clause from `OrderBy`.
    *   Use `@count` for the limit and merge `NamedArgs`.

*   Update `internal/dbadapter` with `JobListParams`:
    *   Fields: `Conditions`, `LimitCount`, `NamedArgs`, `OrderBy`, `Priorities`, `Queues`, `State`.

*   Define `JobListOrderBy` and `SortOrder` in `internal/dbadapter` similarly to `internal/dblist`.

*   Update `Adapter` interface in `internal/dbadapter/db_adapter.go`:
    *   Add `JobList(ctx context.Context, params JobListParams) ([]*dbsqlc.RiverJob, error)`.
    *   Add `JobListTx(ctx context.Context, tx pgx.Tx, params JobListParams) ([]*dbsqlc.RiverJob, error)`.

*   Implement `StandardAdapter` methods:
    *   `JobList`: Open a transaction, call `JobListTx`, commit.
    *   `JobListTx`: Translate `dbadapter.JobListParams` to `dblist.JobListParams`, prepend queue condition if `Queues` is non-empty, call `dblist.JobList`.

*   Ensure SQL generated when `Queues` is populated filters by specified queues, applying both `Queues` and `Conditions` with AND.

*   Maintain compatibility with existing tests in `internal/dbadapter` and `internal/dbadaptertest`. Ensure `TestAdapter` compiles by implementing the `Adapter` interface.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.