Implement the ability for PromQL queries to accept explicit time range parameters and ensure proper functionality for PromQL aggregation operations. Update the test setup utility to prevent automatic table creation, allowing tests to define their own tables as needed.

*   Add the `execute_promql_statement` method to the `Instance` struct in `src/datanode/src/instance/sql.rs`.
    *   Method signature: `execute_promql_statement(&self, promql: &str, start: SystemTime, end: SystemTime, interval: Duration, lookback: Duration, query_ctx: QueryContextRef) -> Result<Output>`.
    *   Ensure the method uses caller-provided `start`, `end`, `interval`, and `lookback` parameters for query execution.
    *   Replace the old `execute_promql(promql, query_ctx)` method with this new implementation.

*   Modify the `setup_test_instance` helper to return a bare instance without pre-creating tables.
    *   Ensure tests create necessary tables explicitly before inserting data or running queries.

*   Add the `check_unordered_output_stream` function to `src/datanode/src/tests/test_util.rs`.
    *   Function signature: `check_unordered_output_stream(output: Output, expected: String)`.
    *   Implement functionality to compare query output to an expected string by sorting lines before comparison.

*   Ensure PromQL aggregation operations are correctly implemented:
    *   `SUM BY (label)` must produce grouped sums with output column named `SUM(table.column)`.
    *   `AVG BY (label)` must produce grouped averages with output column named `AVG(table.column)`.
    *   `COUNT BY (label)` must produce grouped counts with output column named `COUNT(table.column)`.
    *   `sum without (label)` must exclude specified label from the grouping key, with remaining labels as output columns alongside `SUM(table.column)`.
    *   `sum by ()` (empty BY clause) must aggregate all values into a single row with `SUM(table.column)`.
    *   `sum (query)` without BY or WITHOUT clause must aggregate all values into a single row with `SUM(table.column)`.
    *   `sum without ()` (empty WITHOUT clause) must keep all label dimensions as grouping keys, producing one row per unique label combination alongside `SUM(table.column)`.
    *   `ceil()` function must apply ceiling to all numeric value columns with output columns named `ceil(table.column)`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.