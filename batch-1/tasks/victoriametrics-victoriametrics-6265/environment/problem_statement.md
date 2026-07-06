## Description

The statsd protocol parser currently discards the metric type field (counter, gauge, histogram, etc.) that is embedded in every statsd line. As a result, after metrics are ingested, there is no way to distinguish between different metric types — all metrics look the same regardless of whether they are counters, gauges, or timers. This makes it impossible to apply type-specific processing or aggregation rules downstream.

Additionally, the parser silently accepts lines that are missing the required type field, which means malformed input passes through without any error, potentially causing silent data corruption.

Finally, the parser only accepts a single numeric value per line, but some statsd clients support packing multiple values into one line as a batch optimization. This means those packed multi-value lines are currently parsed incorrectly.

## Expected Behavior

- The metric type present in each statsd line should be preserved and attached to the parsed metric as a label with a reserved internal key for the metric type, so downstream aggregation can target specific metric types.
- The metric type label should appear before any user-defined tags in the parsed result.
- Lines that are missing the metric type field should be rejected with a parse error instead of being silently accepted.
- Multiple numeric values packed into a single line (colon-separated) should all be parsed and stored individually. If any packed value is not a valid number, the line should fail to parse.
- Extended protocol fields such as container identifiers and timestamp suffixes should be recognized and silently ignored.

## Why This Matters

Without preserving the metric type, users cannot configure streaming aggregation rules per metric type (e.g., applying "last" aggregation for gauges vs. "sum" for counters). Requiring the type field enforces correct protocol usage and prevents ambiguous input from being silently accepted.
