Implement a concurrency throttling mechanism for multi-stage queries in a distributed query cluster. Manage a per-broker concurrency limit derived from a cluster-wide configuration, and ensure dynamic adjustment to cluster changes.

*   Implement the `MultiStageQueryThrottler` class with the following specifications:
    *   Include a no-argument constructor.
    *   Implement the `init(HelixManager helixManager)` method to read the `CONFIG_OF_MAX_CONCURRENT_MULTI_STAGE_QUERIES` configuration and the list of cluster instances from Helix, computing the initial per-broker permit count.
    *   Compute the per-broker permit count using the formula: `max(1, maxConcurrentQueries * numServers / numBrokers)`.
        *   `numBrokers` is the count of instances whose names start with 'Broker_'.
        *   `numServers` is the count of instances whose names start with 'Server_'.
    *   Implement the `tryAcquire(long timeout, TimeUnit unit)` method to:
        *   Return `true` if a permit is acquired within the specified timeout.
        *   Return `false` if the timeout expires without acquiring a permit.
        *   Always return `true` when throttling is disabled (maxConcurrentQueries <= 0).
    *   Implement the `release()` method to release one permit back to the throttler.
    *   Implement the `availablePermits()` method to return the current number of available permits, which may be negative if the quota was reduced below the number of currently held permits.
    *   Implement the `processClusterChange(HelixConstants.ChangeType changeType)` method to:
        *   Handle `HelixConstants.ChangeType.EXTERNAL_VIEW` by re-reading the cluster instance list and recomputing the per-broker quota, adjusting available permits accordingly.
        *   Handle `HelixConstants.ChangeType.CLUSTER_CONFIG` by re-reading the configuration and adjusting permits based on the delta between new and old quotas.
    *   Ensure the enabled/disabled state of the throttler is determined at startup and remains unchanged by subsequent configuration updates.
        *   If initialized with throttling enabled (maxConcurrentQueries > 0), ignore any config change to a disabled value (<= 0).
        *   If initialized with throttling disabled (maxConcurrentQueries <= 0), ignore any config change to an enabled value (> 0).

*   Ensure `CommonConstants.Helix` exposes a constant named `CONFIG_OF_MAX_CONCURRENT_MULTI_STAGE_QUERIES` for accessing the configuration key.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.