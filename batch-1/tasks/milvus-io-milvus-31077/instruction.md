Update the data coordinator client to properly propagate transport-level errors in index-related operations. Ensure that communication errors are returned to the caller, while responses with error statuses but no transport issues are passed back for inspection.

*   Modify the following methods in `internal/distributed/datacoord/client/client.go` on the `Client` type to handle gRPC transport errors:
    *   `CreateIndex`
    *   `GetSegmentIndexState`
    *   `GetIndexState`
    *   `GetIndexInfos`
    *   `DescribeIndex`
    *   `GetIndexStatistics`
    *   `GetIndexBuildProgress`
    *   `DropIndex`
    *   Ensure each method:
        *   Returns a non-nil Go error when a non-nil transport-level error occurs during the gRPC call.
        *   Returns the response with a nil Go error when the response contains a non-zero status code but no transport-level error.

*   Ensure all other methods in the data coordinator client also return a non-nil Go error when a gRPC transport error occurs:
    *   `GetComponentStates`
    *   `GetTimeTickChannel`
    *   `GetStatisticsChannel`
    *   `Flush`
    *   `AssignSegmentID`
    *   `GetSegmentStates`
    *   `GetInsertBinlogPaths`
    *   `GetCollectionStatistics`
    *   `GetPartitionStatistics`
    *   `GetSegmentInfoChannel`
    *   `GetSegmentInfo`
    *   `GetRecoveryInfo`
    *   `GetRecoveryInfoV2`
    *   `GetFlushedSegments`
    *   `GetSegmentsByStates`
    *   `ShowConfigurations`
    *   `GetMetrics`
    *   `ManualCompaction`
    *   `GetCompactionState`
    *   `GetCompactionStateWithPlans`
    *   `WatchChannels`
    *   `GetFlushState`
    *   `GetFlushAllState`
    *   `DropVirtualChannel`
    *   `SetSegmentState`
    *   `Import`
    *   `UpdateSegmentStatistics`
    *   `UpdateChannelCheckpoint`
    *   `SaveImportSegment`
    *   `UnsetIsImportingState`
    *   `MarkSegmentsDropped`
    *   `BroadcastAlteredCollection`
    *   `CheckHealth`
    *   `GcConfirm`
    *   `ReportDataNodeTtMsgs`
    *   `GcControl`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.