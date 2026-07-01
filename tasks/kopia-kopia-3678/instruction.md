Implement a feature to convert metric snapshots into time series data, allowing analysis of metric trends over time. Ensure the data can be grouped by user and hostname, hostname only, or aggregated across all sources. Provide multiple time resolution options and handle snapshots spanning multiple time buckets proportionally.

*   Implement the `CreateTimeSeries` function in `internal/metrics/metrics_timeseries.go`:
    *   Accept parameters: `context.Context`, slice of `*Snapshot`, `SnapshotValueAggregator`, and `AggregateMetricsOptions`.
    *   Return a `map[string]TimeSeries[TValue]` mapping group keys to time series data.
    *   Group snapshots by `"user@hostname"` by default, or by hostname with `AggregateByHost`, or aggregate all with `AggregateAll`.
    *   Default time resolution to daily if none is specified.

*   Define the `Snapshot` struct in `internal/metrics/metrics_registry.go`:
    *   Fields: `StartTime`, `EndTime`, `User`, `Hostname`, `Counters`, `DurationDistributions`, `SizeDistributions`.

*   Define the `TimeSeries[T]` type as a slice of `TimeSeriesPoint[T]` with fields `Time` and `Value`.

*   Handle snapshot time distribution:
    *   Place full counter value in a single period if entirely within it.
    *   Proportionally distribute counter values across periods if spanning multiple.
    *   Scale `BucketCounters` for distribution metrics proportionally across periods.

*   Return an empty map from `CreateTimeSeries` if the requested metric does not exist.

*   Implement `TimeResolutionFunc` functions:
    *   `TimeResolutionByHour`: Align to UTC hour start.
    *   `TimeResolutionByDay`: Align to UTC day start.
    *   `TimeResolutionByMonth`: Align to month start, accounting for days in each month.
    *   `TimeResolutionByQuarter`: Align to quarter starts (Jan 1, Apr 1, Jul 1, Oct 1).
    *   `TimeResolutionByYear`: Align to year start.
    *   `TimeResolutionByWeekStartingSunday`: Align to week starting Sunday.
    *   `TimeResolutionByWeekStartingMonday`: Align to week starting Monday.

*   Implement `CounterValue`, `DurationDistributionValue`, and `SizeDistributionValue` functions:
    *   Return appropriate value aggregators for counters, duration distributions, and size distributions.
    *   Ensure they return an empty map if the metric name does not exist.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.