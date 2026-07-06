Implement finer-grained latency tracking in the adaptive operation tracker by supporting individual data node and disk granularity. Update the metrics infrastructure to use a generalized resource abstraction for latency histogram maps, allowing partitions, data nodes, and disks to be used interchangeably as map keys.

*   Update `OperationTrackerScope` enum:
    *   Add `DataNode` and `Disk` as values alongside `Datacenter` and `Partition`.
*   Modify `NonBlockingRouterMetrics` class:
    *   Replace partition-level histogram maps with resource-level maps:
        *   `getBlobLocalDcResourceToLatency`: Map<Resource, Histogram>
        *   `getBlobCrossDcResourceToLatency`: Map<Resource, Histogram>
        *   `getBlobInfoLocalDcResourceToLatency`: Map<Resource, Histogram>
        *   `getBlobInfoCrossDcResourceToLatency`: Map<Resource, Histogram>
    *   Ensure maps remain null when the tracker scope is `Datacenter`.
*   Implement resource-specific behavior in `AdaptiveOperationTracker`:
    *   Rename `getPartitionToLatencyMap` to `getResourceToLatencyMap` with signature `getResourceToLatencyMap(RouterOperation routerOperation, boolean isLocal)`.
    *   Return null for `Datacenter` scope.
    *   Throw `IllegalArgumentException` for unsupported operations like `PutOperation`.
    *   For `DataNode` scope:
        *   Use `DataNodeId` to select latency histograms from local or cross-DC maps.
        *   Use per-node histograms for past due decisions and speculative requests.
    *   For `Disk` scope:
        *   Use `DiskId` to select latency histograms from local or cross-DC maps.
        *   Use per-disk histograms for past due decisions and speculative requests.
*   Ensure the past due counter increments for each inflight request exceeding the threshold based on the per-resource histogram quantile cutoff.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.