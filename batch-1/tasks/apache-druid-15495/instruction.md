Fix the handling of multi-stage query engine ingest operations (INSERT and REPLACE) that produce zero rows. Ensure that empty queries complete successfully without errors, and correctly handle tombstone interval calculations for eternity segments.

*   Update the `computeTombstoneIntervalsForReplace` method in `TombstoneHelper.java`:
    *   Ensure it correctly handles cases where the overlap between an existing segment and the replace interval spans all of time (eternity).
    *   Return a single tombstone interval covering eternity when applicable.
    *   Use `Intervals.isEternity(overlap)` to check if the overlap is eternity and add it directly to the result set.

*   Implement query context handling for empty ingest operations:
    *   Introduce `CTX_FAIL_ON_EMPTY_INSERT` as a new constant in `MultiStageQueryContext.java` for the fail-on-empty-insert flag.
    *   Set `DEFAULT_FAIL_ON_EMPTY_INSERT` to `false` by default.
    *   Add `isFailOnEmptyInsertEnabled` method to determine if the fail-on-empty-insert flag is enabled in a given query context.

*   Modify behavior for empty INSERT and REPLACE queries:
    *   Ensure queries producing zero output rows complete successfully without creating data segments if `failOnEmptyInsert` is not enabled.
    *   Trigger an `InsertCannotBeEmptyFault` if `failOnEmptyInsert` is enabled and zero rows are produced, identifying the target datasource.
    *   For REPLACE queries with zero output rows, create tombstone segments for overlapping existing segments to remove stale data.

*   Update test infrastructure:
    *   Allow ingest query test cases to specify an empty expected result row list without requiring an expected datasource name or row signature.
    *   Introduce `FAIL_EMPTY_INSERT_ENABLED_MSQ_CONTEXT` in `MSQTestBase.java` to enable fail-on-empty behavior for tests.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.