Implement a reusable filter component for snapshot garbage collection in the `org.apache.hadoop.ozone.om.snapshot.filter` package. This filter should determine the reclaimability of keys by loading and locking relevant snapshots, ensuring consistency and preventing data loss or inconsistency.

*   Create an abstract generic class `ReclaimableFilter<V>` in `hadoop-ozone/ozone-manager/src/main/java/org/apache/hadoop/ozone/om/snapshot/filter/ReclaimableFilter.java`.
    *   Implement `CheckedFunction<Table.KeyValue<String, V>, Boolean, IOException>` and `Closeable`.
    *   Constructor parameters: `OzoneManager`, `OmSnapshotManager`, `SnapshotChainManager`, nullable `SnapshotInfo currentSnapshotInfo`, `KeyManager`, `IOzoneManagerLock`, and `int numberOfPreviousSnapshotsFromChain`.
    *   Define abstract methods:
        *   `protected abstract String getVolumeName(Table.KeyValue<String, V> keyValue) throws IOException`
        *   `protected abstract String getBucketName(Table.KeyValue<String, V> keyValue) throws IOException`
        *   `protected abstract Boolean isReclaimable(Table.KeyValue<String, V> keyValue) throws IOException`
    *   Implement `public synchronized Boolean apply(Table.KeyValue<String, V> keyValue) throws IOException`.
        *   Load and lock up to `numberOfPreviousSnapshotsFromChain` previous snapshots.
        *   Delegate to `isReclaimable()` for reclaimability check.
        *   Throw `IOException` if `currentSnapshotInfo` volume/bucket mismatch: "Volume and Bucket name for snapshot : {snapshot} do not match against the volume: {volume} and bucket: {bucket} of the key."
        *   Throw `IOException` if any snapshot is inactive: "Unable to load snapshot. Snapshot with table key '/{volume}/{bucket}/{snapshotName}' is no longer active".
        *   Throw `IOException` if any snapshot has uncommitted changes: "Changes made to the snapshot: {snapshotInfo} have not been flushed to the disk."
    *   Expose package-private methods:
        *   `List<SnapshotInfo> getPreviousSnapshotInfos()`
        *   `List<ReferenceCounted<OmSnapshot>> getPreviousOmSnapshots()`
    *   Ensure `getPreviousSnapshotInfos()` and `getPreviousOmSnapshots()` reflect the loaded snapshots after `apply()` is called.
    *   Acquire `SNAPSHOT_GC_LOCK` read locks for loaded snapshots and the current snapshot.
    *   Detect and re-initialize if the snapshot chain changes between `apply()` calls.

*   Add a new lock resource `SNAPSHOT_GC_LOCK` to `OzoneManagerLock.Resource` enum in `hadoop-ozone/common/src/main/java/org/apache/hadoop/ozone/om/lock/OzoneManagerLock.java`.
    *   Define as `SNAPSHOT_GC_LOCK((byte) 8, "SNAPSHOT_GC_LOCK")` after `SNAPSHOT_LOCK((byte) 7)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.