## Description

The NexMark streaming benchmark suite is missing an implementation of Query 7, the "Highest Bid" query. This query is part of the standard NexMark benchmark and is used to evaluate a streaming system's ability to perform windowed aggregations combined with filtering based on a computed aggregate.

The query should group incoming bid events into fixed-duration tumbling time windows and, for each window, identify all bids whose price equals the highest price seen in that window. Bids that fall below the maximum price for their window should be excluded. When multiple bids tie for the highest price in a window, all of them should appear in the results.

## Expected Behavior

- Bid events are grouped into tumbling time windows whose duration is configurable.
- Within each window, the maximum bid price is computed.
- All bids matching that maximum price are returned with their auction ID, price, and bidder.
- Bids with prices below the window maximum are not returned.
- The query integrates into the existing NexMark benchmark launcher alongside the other SQL-based queries.

## Why This Matters

Without this query, the benchmark cannot exercise the "Highest Bid" workload pattern, leaving a gap in coverage for evaluating streaming SQL engines on the NexMark suite. Adding this query completes support for another standard benchmark workload.
