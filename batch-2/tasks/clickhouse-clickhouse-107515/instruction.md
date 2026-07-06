I'm hitting a crash in ClickHouse when querying a table that uses partition-based data organization.

*   A SELECT query on a partitioned MergeTree table (one using PARTITION BY) where the WHERE clause wraps a subquery-based IN check inside a larger expression (e.g. the IN result is compared to a constant) must execute without errors and return the correct matching rows.

*   A SELECT query on a partitioned MergeTree table where a subquery-based NOT IN check is wrapped inside a larger expression must execute without errors and return the rows that are NOT in the subquery result.

*   The same wrapped IN pattern executed against a non-partitioned MergeTree table must continue to work correctly (control case — partition pruning is not involved).

*   A top-level (non-wrapped) IN subquery on a partitioned MergeTree table must continue to return correct results with no regression.

*   A SELECT query on a partitioned MergeTree table where a GLOBAL IN subquery check is wrapped inside a larger expression must execute without errors and return the correct matching rows. GLOBAL IN sets are intentionally never built at partition-pruning time, so the pruner must not attempt to evaluate them.

*   A SELECT query on a partitioned MergeTree table where a GLOBAL NOT IN subquery check is wrapped inside a larger expression must execute without errors and return the correct non-matching rows.

*   A top-level (non-wrapped) GLOBAL IN subquery on a partitioned MergeTree table must continue to return correct results with no regression.

*   The fix must reside in the partition key condition analysis code (src/Storages/MergeTree/KeyCondition.cpp), specifically in the function that walks the monotonic function chain. When that traversal encounters an IN or GLOBAL IN operator, it must stop the chain traversal (return false) rather than attempting to execute the operator's unbuilt set.


*   Interface details: NO INTERFACES NEEDED

The test exercises only SQL query behavior against ClickHouse — no new public C++ functions, classes, or methods are introduced. The fix is internal to the partition key analysis logic in `src/Storages/MergeTree/KeyCondition.cpp`, specifically within the `isKeyPossiblyWrappedByMonotonicFunctionsImpl` function. That function must be modified to detect IN and GLOBAL IN operators and return `false` immediately (ending the monotonic-chain traversal) rather than attempting to evaluate their unbuilt sets.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.