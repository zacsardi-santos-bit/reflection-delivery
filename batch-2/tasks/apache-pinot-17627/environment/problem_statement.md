## Description

When reloading segments in a Pinot table, operators can currently target all segments, a named set of segments, or segments hosted by a specific server. However, there is no way to filter segments by when their data was ingested — for example, "reload everything from the past 30 days." For large tables with time-partitioned data this forces operators to manually identify and enumerate segments, which is error-prone and tedious.

Additionally, when a reload spans multiple servers today, each server generates its own independent job tracking entry. This makes it difficult to treat the entire reload as one logical operation and monitor its status through a single record.

## Expected Behavior

- The segment reload endpoint should accept optional start and end time boundaries (in milliseconds). Only segments whose time range falls within the specified window are reloaded.
- Either the start or end boundary may be omitted to make that side of the window unbounded.
- An option should allow restricting the reload to segments whose data is fully contained within the range (excluding those that only partially overlap the boundary).
- Time-range filtering must be mutually exclusive with server-targeting options — combining them should result in a clear error response.
- Invalid timestamp values and illogical ranges (start ≥ end) must be rejected with an appropriate error response.
- When time-range or multi-server reloads are triggered, a single unified job entry per table should be stored for tracking, capturing the complete mapping of servers to segments.
- The status-reporting component should use this stored mapping directly when it exists, rather than re-deriving it from cluster state.

## Why This Matters

Time-range-based reloads make it possible to refresh data within a known window without manual segment enumeration. Unified job tracking simplifies operational monitoring: a single entry captures the whole operation, and status checks can use the stored mapping directly without querying the cluster.
