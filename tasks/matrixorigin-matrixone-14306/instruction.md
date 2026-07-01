Implement a validation check for database existence when creating or dropping user-defined functions using a database-qualified name. Ensure that operations fail immediately with a clear error message if the specified database does not exist.

*   Implement the function `checkDatabaseExistsOrNot(ctx context.Context, bh BackgroundExec, dbName string) (bool, error)` in `pkg/frontend/authenticate.go`.
    *   Return `(true, nil)` if the database exists.
    *   Return `(false, nil)` if the database does not exist.
    *   Return `(false, error)` if there is a query failure.
*   Update the user-defined function creation handler:
    *   Validate the existence of the target database before proceeding.
    *   Return a non-nil error if the database does not exist.
    *   Fail the operation with an 'invalid database' error message if the database is missing.
*   Update the user-defined function drop handler:
    *   Validate the existence of the target database before proceeding.
    *   Return a non-nil error if the database does not exist.
    *   Fail the operation with an 'invalid database' error message if the database is missing.
*   Ensure that operations succeed when the specified database exists:
    *   Allow user-defined function creation to proceed normally.
    *   Allow user-defined function deletion to proceed normally.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.