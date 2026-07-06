## Description

Ray currently reports per-component memory metrics (resident set size and unique set size) only in megabytes. This is inconvenient for monitoring systems, dashboards, and alerting rules that work natively in bytes — users have to manually multiply by 1,000,000 to get accurate byte values. Additionally, one of the shared memory metrics uses a legacy naming convention that does not match the byte-unit naming style used elsewhere.

## Expected Behavior

- New byte-unit variants of the component resident set size and unique set size metrics should be available alongside the existing megabyte metrics, so consumers can choose the appropriate unit for their use case.
- The byte-unit metrics should report the exact raw byte values with no lossy unit conversion.
- The existing megabyte metrics should continue to be emitted unchanged for backward compatibility.
- The shared memory metric should be renamed to follow the consistent byte-unit naming convention used by the new metrics.

## Why This Matters

Monitoring tools and alerting systems typically operate in bytes for memory thresholds. Having to convert megabytes to bytes in every query or rule is error-prone and adds unnecessary complexity. Providing byte-precision metrics directly from the reporter agent eliminates this overhead and improves the accuracy of memory-based alerts.
