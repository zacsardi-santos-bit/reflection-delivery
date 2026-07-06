## Description

River's PostgreSQL driver fails when the database connection is configured to use a query protocol that doesn't support extended binary encoding for parameters — the kind required by connection poolers running in transaction pooling mode. In these modes, the driver sends byte-slice arguments without any type context, causing PostgreSQL to interpret JSON job arguments as binary data rather than JSON. This results in invalid syntax errors that prevent jobs from being enqueued or processed at all.

## Expected Behavior

- When the driver detects that the underlying connection uses a text-based or simple query execution mode, it should automatically convert JSON byte arguments to a type that PostgreSQL can accept as JSON text.
- Arguments that are explicitly intended as binary data (via an appropriate SQL cast) must not be affected.
- Connections using the standard prepared-statement execution mode should continue working exactly as before — no behavioral change in the default case.
- The driver should handle the case where a connection's mode information is unavailable (e.g., in test transactions that don't expose a live connection), falling back to the standard prepared-statement behavior.

## Why This Matters

Many production deployments run behind connection poolers like PgBouncer configured in transaction pooling mode. This mode requires a simple query protocol that has no support for type OIDs. Without this fix, River cannot be used in those environments at all. Users should be able to configure their connection pool for transaction pooling mode and have River work transparently.
