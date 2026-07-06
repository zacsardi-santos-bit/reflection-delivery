## Description

The V1 archived timeline currently has several limitations when loading historical commit data from archive log files. First, it only supports loading instants from a start timestamp forward — there is no way to specify both a start and end boundary to read a bounded slice of archived history. Second, there is no way to filter the archived data to only specific archive log files, forcing a full scan even when only a subset of files is needed. Third, and most critically, when loading instant details the implementation tracks only one payload per commit timestamp, meaning that if you load a requested, in-flight, and completed record for the same commit, the earlier states are silently overwritten. This makes it impossible to retrieve lifecycle-specific metadata (for example, the compaction plan that was originally requested vs. the final completed commit record).

## Expected Behavior

- It should be possible to construct an archived timeline with a closed start-and-end time range so that only instants within that range are loaded.
- It should be possible to filter by lifecycle state (requested, in-flight, completed, or all states) when loading instants, so that callers can efficiently retrieve only the state they care about.
- It should be possible to target specific archive log files by path, so that only those files are read.
- Retrieving instant details must be state-aware: the same timestamp can have independent byte payloads for its requested, in-flight, and completed states, and each must be retrievable independently without overwriting the others.

## Why This Matters

Without these capabilities, tools that need to audit historical commit states (for example, inspecting the original compaction plan for a now-completed compaction) cannot do so correctly because the state-specific metadata is lost. Adding bounded range and log-file filtering also improves performance for queries that only need a small slice of the archive.
