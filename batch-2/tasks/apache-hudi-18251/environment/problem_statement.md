## Description

When running clustering on large Apache Hudi tables, there is currently no way to organize the clustering plan based on the table's commit history. Existing clustering plan strategies work on file slices without considering when individual files were written, making it impossible to cluster incrementally — processing only the files added since the last clustering run.

Users need a commit-aware clustering plan strategy that:
- Iterates through completed commits chronologically
- Groups files from each commit by partition, placing files from different partitions into separate clustering groups
- Respects a configurable per-group size limit, splitting files into multiple groups when necessary
- Excludes file groups that have already been replaced by prior replace commits
- Handles both regular commits and delta commits (for merge-on-read tables with log files)
- Stores a progress checkpoint (the last processed commit time) in the clustering plan's extra metadata, so subsequent clustering runs can resume from where the previous run left off

## Expected Behavior

- When no commits exist in the timeline, the strategy should produce no clustering plan
- When eligible files are found, the strategy generates a clustering plan grouping files by partition and size
- Files from replaced file groups must not appear in the clustering plan
- After plan generation, the extra metadata must contain a checkpoint key indicating the last commit that was processed
- When no commits were processed, the checkpoint key must be absent (null value) in the extra metadata

## Configuration

A new configuration option should allow users to specify an earliest commit time (exclusive) so the strategy only considers commits added after that point. This supports incremental clustering workflows where users want to start clustering from a known checkpoint.

## Why This Matters

Without this strategy, users cannot efficiently run incremental clustering on large tables with many commits. Re-clustering all file slices every time is expensive and redundant. A commit-based approach allows teams to continuously cluster only newly written data, keeping table performance optimal over time.
