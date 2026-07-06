## Description

When a Flink task recovers from a checkpoint, the system restores potentially large amounts of state from different storage tiers — in-memory buffers, local disk files, and remote/cloud storage. Currently, there is no way to track or measure how much data was restored and from which storage location it came from. This makes it very difficult to diagnose slow recovery or understand the cost breakdown of checkpoint restoration.

## Expected Behavior

- Each type of state handle should be able to categorize and report its size by storage location (local memory, local disk, remote/cloud, or unknown).
- There should be a utility to compute the total size of all files within a directory (recursively), returning zero for empty directories.
- Directory-based state handles should be created with their sizes pre-computed.
- After a task finishes restoring from a checkpoint, the task initialization metrics should include a breakdown of how much data was restored from each storage tier, using a consistent metric name prefix for all per-location size entries.
- The component responsible for restoring backends should propagate the size-collection context through the restoration process.

## Why This Matters

Operators and developers currently have no visibility into the size or location breakdown of restored state after a checkpoint recovery. This feature provides the missing observability, enabling teams to understand restoration performance, compare storage tier costs, and identify bottlenecks when recovery is slower than expected.
