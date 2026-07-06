Implement a rollback mechanism for Merge-on-Read tables that correctly handles log files based on the table version. Ensure that rollback requests are grouped and executed according to the specified logic for both older and newer table versions.

*   Implement `groupRollbackRequestsBasedOnFileGroup` in `RollbackUtils.java`:
    *   Accept a `List<HoodieRollbackRequest>`.
    *   Merge log-only requests (empty `filesToBeDeleted`) sharing the same partition, fileId, and latestBaseInstant into a single request with combined `logBlocksToBeDeleted`.
    *   Ensure requests with `filesToBeDeleted` are not merged with log-only requests.
    *   Split mixed requests into separate base-file-only and log-only requests.
    *   Normalize null `partitionPath` to an empty string.

*   Implement `groupSerializableRollbackRequestsBasedOnFileGroup` in `RollbackUtils.java`:
    *   Accept a `List<SerializableHoodieRollbackRequest>`.
    *   Apply the same grouping logic as `groupRollbackRequestsBasedOnFileGroup`.

*   Update `BaseRollbackHelper.maybeDeleteAndCollectStats`:
    *   For table version >= EIGHT, delete log files in `filesToBeDeleted` and include them in `successDeleteFiles`.
    *   For table version < EIGHT, preserve log files and append a rollback command block, consolidating multiple requests for the same file group.
    *   Always delete base files listed in `filesToBeDeleted`.
    *   Return an empty `HoodieRollbackStat` for empty rollback requests.

*   Update `MarkerBasedRollbackStrategy.getRollbackRequests`:
    *   For table version >= EIGHT, return one `HoodieRollbackRequest` per log file with the full log file path in `filesToBeDeleted`.
    *   For table version < EIGHT, return one request per file group with combined log files in `logBlocksToBeDeleted`.

*   Implement `createLogFileMarker` in `FileCreateUtils.java`:
    *   Accept `HoodieTableMetaClient`, `partitionPath`, `instantTime`, and `logFileName`.
    *   Use `IOType.CREATE` for table version >= EIGHT and `IOType.APPEND` for older versions.
    *   Return the created marker file path string.

*   Implement `withLogMarkerFile` in `HoodieTestTable.java`:
    *   Create a log file marker for the current instant time using `createLogFileMarker`.
    *   Return `this` for method chaining.

*   Implement the overloaded `withLogFile` in `HoodieTestTable.java`:
    *   Accept `partitionPath`, `fileId`, `instantTime`, and `versions`.
    *   Create log files using the provided `instantTime`.
    *   Ensure the existing `withLogFile(partitionPath, fileId, versions)` delegates to this method using `currentInstantTime`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.