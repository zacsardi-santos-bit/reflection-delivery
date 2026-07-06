Implement migration awareness in the keeper state checker to prevent disruption during datacenter migrations. Ensure the checker detects migration states and refrains from sending correction commands when a migration is in progress.

*   Update `DefaultKeeperManager` to support dependency injection:
    *   Implement `setMetaCache(DcMetaCache metaCache)` to inject a `DcMetaCache` instance for migration detection.
    *   Implement `setClusterShardExecutor(KeyedOneThreadMutexableTaskExecutor<Pair<String, String>> clusterShardExecutor)` to inject a keyed executor for serialized correction jobs.

*   Extend `DcMetaCache` interface:
    *   Implement `getShardRedises(String clusterId, String shardId)` to return a `List<RedisMeta>` of known redis instances for a shard.
    *   Implement `isCurrentDcPrimary(String clusterId)` to return a boolean indicating if the current datacenter is the primary for a cluster.

*   Modify `KeeperStateAlignChecker` behavior in `doCheckShard`:
    *   During normal operation, if `isCurrentDcPrimary` returns true and the keeper master matches a redis instance from `getShardRedises`, issue correction commands to align keeper state.
    *   During a primary DC migration, if `isCurrentDcPrimary` returns true but the keeper master does not match any instance from `getShardRedises`, query all keepers for their state but do not send correction commands.
    *   During an other-DC migration, similarly, if `isCurrentDcPrimary` returns true but the keeper master does not match any known shard redis, query all keepers for their state but do not send correction commands.

*   Ensure migration checks are performed:
    *   When evaluating individual keeper info responses to avoid unnecessary corrections.
    *   Before submitting any correction job to ensure no corrections occur during migration.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.