## Description

The SQL driver capability system can currently express whether a database supports update operations inside common table expressions, but it has no way to indicate whether a database supports row-level locking. This matters because different databases require different strategies to safely handle conditional updates — some can lock individual rows during a read, while others require full transaction-level isolation instead.

Additionally, the MySQL driver capability is incorrectly configured: MySQL does not support updates within CTEs, but this was previously marked as supported.

## Expected Behavior

- The SQL capability descriptor should include a field indicating whether the database supports row-level locking.
- MySQL should be declared as NOT supporting CTE-with-update, and as supporting row-level locking.
- PostgreSQL should be declared as supporting both CTE-with-update and row-level locking.
- SQLite should be declared as NOT supporting row-level locking (nor CTE-with-update).

## Why This Matters

Without the ability to advertise row-level locking support, the system cannot choose the correct strategy for performing safe conditional updates on databases like MySQL or SQLite, leading to incorrect behavior or failed queries at runtime. Fixing the MySQL CTE capability flag is also needed to prevent invalid SQL from being generated for MySQL databases.
