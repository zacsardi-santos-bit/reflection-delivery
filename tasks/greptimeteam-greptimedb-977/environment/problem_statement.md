## Description

The current interface for executing PromQL queries against the database does not allow callers to specify the time range parameters — start time, end time, step interval, and lookback window. These values are currently hardcoded internally, making it impossible for different clients or tests to query over different time windows. As a result, the PromQL execution interface is too rigid for real-world use and cannot support varied query scenarios.

## Expected Behavior

- Callers should be able to explicitly specify the start time, end time, step interval, and lookback duration when executing a PromQL query.
- The system should correctly evaluate PromQL aggregation operations — including grouping metrics by label sets, excluding labels from the grouping key (with either a specific list or an empty list), and computing sums, averages, and counts across groups.
- Results from PromQL aggregation queries should include appropriately named output columns reflecting the operation applied and the source table and value column.
- The test setup utility should not auto-create tables on behalf of tests; each test should be responsible for creating the tables it needs.

## Why This Matters

Without the ability to control the query time window, the PromQL interface is impractical for applications that need to query different time ranges. Additionally, aggregation support (grouping, excluding, summarizing) is essential for typical metric analysis workflows that users of Prometheus-compatible databases rely on. Fixing both of these issues enables the system to support realistic PromQL workloads.
