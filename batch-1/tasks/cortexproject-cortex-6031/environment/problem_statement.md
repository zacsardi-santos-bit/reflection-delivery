## Native Histogram Support in the Querier

### Description

Cortex does not correctly handle native histogram data types when querying from block storage. When data has been stored as native histograms (both integer and float variants), querying that data fails or returns incorrect results because the internal iterator layer does not properly handle the case where the query engine requests float histogram representations of integer histogram chunks.

This is a real problem because modern Prometheus query engines commonly request data as float histograms regardless of how it was stored, meaning any native histogram data stored in Cortex is effectively unqueryable.

### Expected Behavior

- Native histogram series (both integer and float histogram variants) should be correctly retrieved from block storage when queried through the store gateway path
- Overlapping histogram series returned from multiple storage nodes should be correctly deduplicated and merged
- All existing resource limits (per-query chunk limits, byte limits, series limits) should apply equally to histogram chunks and regular float chunks
- The query engine should be able to request float histogram representations of integer histogram chunks and receive correct converted values
- Histogram-specific aggregation operations should return correct results when run against histogram data stored in Cortex

### Why This Matters

Native histograms are a key feature of modern Prometheus-compatible systems. Without correct support in the querier, users who store histogram metrics in Cortex cannot query them reliably. This blocks adoption of native histograms for Cortex users.
