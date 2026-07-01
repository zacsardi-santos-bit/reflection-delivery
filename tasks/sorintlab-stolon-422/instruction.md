Improve the reliability of integration tests by replacing fixed sleep delays with a robust synchronization mechanism for PostgreSQL replication. Implement a system to open replication protocol connections, query the primary's write-ahead log position, and ensure all keepers synchronize to this position within a timeout.

*   Update the PostgreSQL LSN conversion function:
    *   Rename the function in `pkg/postgresql/utils.go` from `pgLsnToInt` to `PGLsnToInt` to make it exported.
    *   Ensure all internal calls to the old function name are updated to use `PGLsnToInt`.

*   Modify the Querier interface:
    *   Add a `ReplQuery(query string, args ...interface{}) (*sql.Rows, error)` method to support replication queries.

*   Implement functions for querying and synchronizing log positions:
    *   `GetSystemData(q Querier) (*pg.SystemData, error)`:
        *   Execute `IDENTIFY_SYSTEM` via `ReplQuery`.
        *   Convert the `XLogPos` LSN string to uint64 using `PGLsnToInt`.
        *   Return an error if no rows are returned.
    *   `GetXLogPos(q Querier) (uint64, error)`:
        *   Call `GetSystemData` and return the `XLogPos` value.
        *   Return 0 and an error if `GetSystemData` fails.
    *   `WaitClusterSyncedXLogPos(keepers []*TestKeeper, xLogPos uint64, e *store.Store, timeout time.Duration) error`:
        *   Ensure each keeper's `db.Status.XLogPos` is >= `xLogPos`.
        *   Return an error if synchronization is not achieved within the timeout.

*   Update `TestKeeper` and `TestProxy` structures:
    *   Add an `rdb (*sql.DB)` field for replication connections using `pgReplUsername` and `pgReplPassword`.
    *   Implement `ReplQuery` methods for both `TestKeeper` and `TestProxy`.

*   Adjust constructor functions for replication support:
    *   `NewTestProxy` and `NewTestKeeperWithID` must initialize `rdb` with replication parameters.
    *   Pass `pgReplUsername` and `pgReplPassword` to `NewTestProxy` as additional parameters.

*   Update integration test call sites:
    *   Replace `time.Sleep` calls with a sequence that fetches the primary `XLogPos` using `GetXLogPos` and passes it to `WaitClusterSyncedXLogPos`.
    *   Ensure all calls to `NewTestProxy` include `pgReplUsername` and `pgReplPassword` as the 6th and 7th arguments.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.