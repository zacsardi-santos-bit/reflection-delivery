## Description

The SQL query receiver currently has only a skeleton implementation. It cannot actually collect metrics from a database — the metrics receiver creation function is a no-op that returns nothing. Additionally, the metric configuration lacks the flexibility to describe how query result values should be interpreted, and there is no validation of the configuration before startup, meaning misconfigured receivers fail silently or produce confusing runtime errors.

## Expected Behavior

- The receiver should be able to execute SQL queries against a database and convert each result row into a separate OpenTelemetry metric.
- Each metric should support being represented as either a gauge or a cumulative/delta sum, with values being either integers or floating-point numbers.
- Additional per-metric configuration options should be supported: a monotonicity flag for sums, optional attribute columns to tag data points, and optional description and unit fields.
- The configuration should be fully validated at startup. Missing required fields (such as the driver, datasource, query SQL, or metric name) should produce clear error messages identifying exactly which field is absent.
- Invalid values for the new type/aggregation options should be rejected with descriptive error messages naming the unsupported value.
- Incompatible combinations (such as specifying aggregation for a gauge metric) should be caught and reported.
- When a single metric has multiple configuration errors, all errors should be reported together rather than just the first.

## Why This Matters

Without this core functionality, the SQL query receiver cannot be used to collect any metrics from a database. Users who deploy the receiver today get no data, with no indication of what is wrong. Proper configuration validation and meaningful error messages are essential for operators to understand and fix misconfigurations quickly.
