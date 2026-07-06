Implement a new cluster aggregator class to aggregate storage statistics from a MySQL-backed stats store. Ensure the class can produce both a raw combined view and a valid selected view of storage stats. Update the partition class storage stats object to support instantiation without arguments and allow deep copying.

*   Implement the `MySqlClusterAggregator` class with the following specifications:
    *   Constructor: `MySqlClusterAggregator(long relevantTimePeriodInMinutes)`.
    *   Method: `long sumPhysicalStorageUsage(Map<Short, Map<Short, ContainerStorageStats>> accountStorageStatsMap)` to return the sum of all `physicalStorageUsage` values.
    *   Method: `void mergeAccountStorageStatsMap(Map<Short, Map<Short, ContainerStorageStats>> result, Map<Short, Map<Short, ContainerStorageStats>> current)` to merge storage stats maps.
    *   Method: `Map<Short, Map<Short, ContainerStorageStats>> aggregateHostAccountStorageStats(Map<Long, Map<Short, Map<Short, ContainerStorageStats>>> storageStatsMap)` to return a flattened map of aggregated stats.
    *   Method: `Map<String, Map<Short, Map<Short, ContainerStorageStats>>> aggregateHostPartitionClassStorageStats(Map<String, Map<Long, Map<Short, Map<Short, ContainerStorageStats>>>> storageStatsMap)` to aggregate partition class stats.
    *   Method: `Pair<AggregatedAccountStorageStats, AggregatedAccountStorageStats> aggregateHostAccountStorageStatsWrappers(Map<String, HostAccountStorageStatsWrapper> statsWrappers) throws IOException` to return a pair of aggregated account stats.
        *   Ensure the first element is the raw combined sum and the second is the valid aggregated stats.
        *   Apply selection logic based on timestamp and physical storage usage.
    *   Method: `Pair<AggregatedPartitionClassStorageStats, AggregatedPartitionClassStorageStats> aggregateHostPartitionClassStorageStatsWrappers(Map<String, HostPartitionClassStorageStatsWrapper> statsWrappers) throws IOException` to return a pair of aggregated partition class stats.
        *   Apply the same selection logic as for account storage stats.

*   Update the `HostPartitionClassStorageStats` class:
    *   Provide a no-argument constructor: `HostPartitionClassStorageStats()`, initializing an empty internal storage stats map.
    *   Provide a copy constructor: `HostPartitionClassStorageStats(HostPartitionClassStorageStats other)` for deep copying.
    *   Ensure existing constructor: `HostPartitionClassStorageStats(Map<String, Map<Long, Map<Short, Map<Short, ContainerStorageStats>>>> storageStats)` remains functional.
    *   Method: `Map<String, Map<Long, Map<Short, Map<Short, ContainerStorageStats>>>> getStorageStats()` to retrieve storage stats.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.