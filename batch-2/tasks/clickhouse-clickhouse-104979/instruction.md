I'm hitting a crash when I try to filter the detached tables system view by UUID.

*   When querying system.detached_tables with a UUID-based predicate (such as WHERE uuid = ... or WHERE uuid != ...), the query must complete successfully and return the correct row count without throwing a LOGICAL_ERROR.

*   If at least one table is detached and the UUID predicate matches it, the count returned must reflect the correct number of matching detached tables.

*   Querying system.detached_tables with a UUID filter must not produce an internal error about mismatched column lengths (i.e., 'uuid' column having 0 rows while the table-name column has 1 or more rows).

*   The fix must apply to databases using the Atomic engine, where detached tables have UUIDs assigned to them.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.