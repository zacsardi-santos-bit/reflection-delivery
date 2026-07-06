Update the SQL datastore plugin's configuration method to accept a context parameter, ensuring consistency with Go's standard API conventions for I/O-bound operations. Modify all call sites to pass a context, and maintain existing error handling behavior.

*   Modify the Configure method on the SQL datastore Plugin struct:
    *   Change the method signature to `Configure(ctx context.Context, hclConfiguration string) error`.
    *   Ensure it parses the HCL configuration payload into an internal config struct and opens database connections.
*   Update all existing callers of the Configure method:
    *   Ensure the catalog loader, test helpers, and the fake datastore pass a context.Context as the first argument.
*   Maintain existing error handling behavior:
    *   Return a non-nil error for unsupported database types (e.g., 'wrong').
    *   Return a non-nil error with the message 'datastore-sql: invalid mysql config: missing parseTime=true param in connection_string' for invalid MySQL connection strings.
    *   Return a non-nil error with the message 'datastore-sql: connection_string must be set' when the MySQL database type is specified without a connection_string.
*   Ensure successful configuration for valid database configurations:
    *   Return nil when called with a valid SQLite3 database configuration.
    *   Return nil when called with a valid MySQL configuration.
    *   Return nil when called with a valid PostgreSQL configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.