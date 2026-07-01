Create a mock implementation of the Metrics interface in the SQL package to enable unit testing of database operations. Ensure the mock supports setting expectations for metrics calls and that all database operations correctly log and record metrics.

*   Create a file named `mock_metrics.go` in `pkg/gofr/datasource/sql/`.
*   Implement a mock type `MockMetrics` that fulfills the Metrics interface:
    *   Methods: `RecordHistogram(ctx context.Context, name string, value float64, labels ...string)`, `IncrementCounter(ctx context.Context, name string, labels ...string)`, `DeltaUpDownCounter(ctx context.Context, name string, value float64, labels ...string)`, `SetGauge(name string, value float64)`.
*   Provide a constructor function `NewMockMetrics(ctrl *gomock.Controller) *MockMetrics` to create a `MockMetrics` instance.
*   Integrate with `go.uber.org/mock/gomock` to allow setting expectations using `mockMetrics.EXPECT().RecordHistogram(...)`.
*   Ensure the package declaration in `mock_metrics.go` is `package sql`.

*   When `DB.Query` is called, ensure `Metrics.RecordHistogram` is invoked with `app_sql_stats` and labels including the SQL verb.
*   When `DB.Exec` is called, ensure `RecordHistogram` is invoked with `app_sql_stats` and labels including the SQL verb.
*   For `DB.QueryRow`, `DB.QueryRowContext`, and `DB.Prepare`, call `RecordHistogram` with `app_sql_stats` and the 'type' label.
*   For transactional operations (`Tx.Query`, `Tx.Exec`, etc.), call `RecordHistogram` with `app_sql_stats` without additional 'type' labels.
*   Emit debug-level log messages for all database operations with appropriate prefixes:
    *   Use 'Query', 'QueryRow', 'QueryRowContext', 'Exec', 'ExecContext', 'Prepare' for DB operations.
    *   Use 'TxExec' for `Tx.Exec`, 'TxCommit COMMIT' for `Tx.Commit`, 'TxRollback ROLLBACK' for `Tx.Rollback`.

*   Implement `DB.Begin()` to return `(*Tx, error)`, ensuring `Tx` is non-nil on success and error is nil.
*   Implement `DB.HealthCheck()` to return `*datasource.Health` with:
    *   Status 'UP' and connection details when connected and ping succeeds.
    *   Status 'DOWN' with only the 'host' key when connection is nil or ping fails.
*   Implement `getDBConnectionString` to return connection strings for MySQL and PostgreSQL, and `errUnsupportedDialect` for others.
*   Implement `getDBConfig` to read environment variables and map them to `DBConfig` fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.