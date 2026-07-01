Refactor the managed database controller to improve testability and error handling. Extract SQL operations into standalone functions and update the reconciler's error handling to reflect failures in the managed database object's status rather than propagating errors.

*   Implement the `detectDatabase` function:
    *   Query the `pg_database` system catalog using the SQL: `SELECT count(*) FROM pg_database WHERE datname = $1`.
    *   Return `true` if the count is greater than 0, otherwise return `false`.
*   Implement the `createDatabase` function:
    *   Execute a `CREATE DATABASE` statement with identifiers quoted using `pgx.Identifier{}.Sanitize()`.
    *   Ensure fields are ordered as: database name, OWNER, TABLESPACE, ALLOW_CONNECTIONS, CONNECTION LIMIT, IS_TEMPLATE.
*   Implement the `updateDatabase` function:
    *   Execute `ALTER DATABASE` statements for each property in the following order:
        1. `ALLOW_CONNECTIONS` if set.
        2. `CONNECTION LIMIT` if set.
        3. `IS_TEMPLATE` if set.
        4. `OWNER` if non-empty.
        5. `TABLESPACE` if non-empty.
    *   Quote identifiers using `pgx.Identifier{}.Sanitize()`.
*   Implement the `dropDatabase` function:
    *   Execute `DROP DATABASE IF EXISTS <name>`, quoting the database name with `pgx.Identifier{}.Sanitize()`.
*   Update the `DatabaseReconciler` struct:
    *   Type the `instance` field as `instanceInterface` instead of a concrete `*postgres.Instance` pointer.
    *   Implement `succeededReconciliation` to set `Status.Ready` to `true` and `Status.Error` to an empty string.
    *   Implement `failedReconciliation` to set `Status.Ready` to `false` and `Status.Error` to the error message.
*   Define the `instanceInterface` with at least the following methods:
    *   `GetSuperUserDB() (*sql.DB, error)`
    *   `GetClusterName() string`
    *   `GetPodName() string`
    *   `GetNamespaceName() string`
*   Update the `postgres.Instance` type:
    *   Add methods `GetClusterName()`, `GetPodName()`, and `GetNamespaceName()` to satisfy `instanceInterface`.
*   Ensure the `Reconcile` method:
    *   Does not return an error on SQL operation failures.
    *   Updates the database Kubernetes object's `Status` with `Ready=false` and the error message.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.