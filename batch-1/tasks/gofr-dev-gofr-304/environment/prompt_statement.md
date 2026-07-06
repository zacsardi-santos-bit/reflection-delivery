I'm working on the SQL database wrapper package in a Go framework. The wrapper already implements all the standard database operations — running queries, executing statements, preparing statements, starting transactions, committing and rolling back — and each operation records metrics and logs at debug level. There's also a health check that reports connection status and pool statistics.

The problem is that the metrics dependency is defined as an interface in the package, but there's no mock for it. When I try to add unit tests that verify the metrics are recorded correctly for each operation, the code doesn't compile because the mock type doesn't exist.

I need a mock implementation of the metrics interface added to this package so that the test code can use it. The mock needs to support setting expectations on all the interface methods, especially the histogram recording method that's called by every SQL operation. The tests check that the correct metric name and appropriate labels are used for each operation type.

Specifically, the tests also verify that:
- Each database operation emits a debug log containing the operation name followed by the query string.
- Transaction-level execution uses a distinct log prefix that differs from the non-transactional execution prefix.
- Transaction commit and rollback operations each use their own specific log prefixes that reflect the action being performed.
- Database operations (non-transactional) include a label in the metric identifying the SQL verb (SELECT, INSERT, etc.), while transaction operations do not include this label.
- The health check reports an up status with connection pool statistics when the database is reachable, and a down status otherwise.
- The connection string builder produces the correct format for MySQL and PostgreSQL dialects and returns an error for unsupported ones.
