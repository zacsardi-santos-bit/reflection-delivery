I'm working on the metrics system and I need to add the ability to convert a set of metric snapshots into time series data so we can track how values change over time. Each snapshot has a start time, end time, user, hostname, and metric values (counters and data-distribution histograms).

The key things I need:

First, a way to build a time series from a list of snapshots for a chosen metric — counters, duration distributions, or size distributions — grouped by user-and-host, host only, or everything combined into one aggregate. When no grouping is specified, it should default to grouping by user and hostname together.

Second, when a snapshot spans multiple time buckets (e.g., a snapshot running from 12:30 to 15:30 covers three hourly buckets), its metric value should be proportionally distributed across each bucket based on how much time falls in that bucket. The same proportional logic should apply to distribution histogram bucket counts.

Third, I need several pre-built time resolution options: hourly, daily (the default when none is provided), weekly starting on either Sunday or Monday, monthly, quarterly, and yearly. Each resolution function takes a point in time and returns the start and end of the period it belongs to — and should behave correctly at boundaries (the last nanosecond before the period end should still fall in the current period, not the next one).

Finally, if the requested metric name doesn't exist in the snapshots, the result should be an empty collection rather than failing.
