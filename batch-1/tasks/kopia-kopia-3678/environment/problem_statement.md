## Description

The metrics system can record snapshots of counters and data-distribution measurements captured across different users and machines, but there is currently no way to analyze how these metrics evolve over time. We need the ability to generate time series data from a collection of snapshots so that trends can be observed at various time granularities.

## Expected Behavior

- Given a set of snapshots (each with a start time, end time, user, hostname, and metric values), it should be possible to produce time series data grouped by the source of the measurement — by individual user and hostname, by hostname alone, or aggregated across all sources.
- When a snapshot's time range is entirely contained within a single time bucket, its full value should appear in that bucket.
- When a snapshot spans multiple time buckets, its value should be proportionally distributed across each bucket based on how much of the snapshot's total duration falls within that bucket. This applies to both plain counters and distribution-type metrics.
- Several time resolution options should be available: hourly, daily, weekly (with a choice of week start day), monthly, quarterly, and yearly. When no resolution is specified, daily should be the default.
- For any time resolution, the period-boundary function should be consistent: any point in time within a period should map to the same start and end timestamps, and the function should be idempotent when applied to a period's own start.
- If a requested metric does not exist in the snapshots, the result should be an empty collection rather than an error.

## Why This Matters

Without time series support, operators can only see point-in-time snapshots of metrics and cannot identify trends, growth rates, or periodic patterns across hours, days, weeks, or months. This feature is foundational for building dashboards and reports that show how backup activity and resource usage change over time.
