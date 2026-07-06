Implement enhancements to the `ArchivedTimelineV1` class to support more flexible loading of historical commit data from archive log files. Add constructors and methods to filter by time range, lifecycle state, and specific log files, ensuring state-aware retrieval of instant details.

*   Update `ArchivedTimelineV1` class:
    *   Implement a constructor `ArchivedTimelineV1(HoodieTableMetaClient metaClient, String startTs, String endTs)` to load only COMPLETED instants within the closed range [startTs, endTs].
    *   Implement a constructor `ArchivedTimelineV1(HoodieTableMetaClient metaClient, String startTs, String endTs, Option<HoodieInstant.State> state)` to load instants of a specified state within [startTs, endTs].
    *   Implement a constructor `ArchivedTimelineV1(HoodieTableMetaClient metaClient, Set<String> logFiles)` to load only COMPLETED instants from specified archive log files.
    *   Implement a constructor `ArchivedTimelineV1(HoodieTableMetaClient metaClient, Set<String> logFiles, Option<HoodieInstant.State> state)` to load instants of a specified state from specified log files.
    *   Ensure `getInstantDetails(HoodieInstant instant)` retrieves state-specific details independently, returning an empty `Optional` if the (timestamp, state) combination was not loaded.
    *   Ensure CLEAN and ROLLBACK action types only load COMPLETED metadata; `getInstantDetails` must return empty for REQUESTED or INFLIGHT states.
    *   Ensure REPLACE_COMMIT action type does not load INFLIGHT metadata; handle REQUESTED and COMPLETED states separately.
    *   Implement the no-argument constructor `ArchivedTimelineV1(HoodieTableMetaClient metaClient)` to load all instants without loading byte details into memory.

*   Add `ClosedClosedTimeRangeFilter` inner class to `HoodieArchivedTimeline`:
    *   Implement `ClosedClosedTimeRangeFilter(String startTs, String endTs)` to provide a closed-closed range check.
    *   Implement `boolean isInRange(String instantTime)` and `boolean isInRange(HoodieInstant instant)` methods.

*   Implement additional methods:
    *   `void loadCompactionDetailsInMemory(String ts)` to load compaction plan bytes for the INFLIGHT state of a compaction instant.
    *   `void loadInstantDetailsInMemory(String startTs, String endTs)` to load details for all available states of all action types within [startTs, endTs], excluding REPLACE_COMMIT inflight and CLEAN/ROLLBACK requested/inflight states.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.