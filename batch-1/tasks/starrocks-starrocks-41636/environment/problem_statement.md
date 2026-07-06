## Description

The lock-free ID tracking data structure used internally by multi-threaded components grows without bound over its lifetime. It accumulates stale entries — IDs that are no longer in use — with no mechanism to reclaim that space. In long-running systems that create and destroy many concurrent operations, this results in unbounded memory growth and increasing overhead when iterating over the list.

## Expected Behavior

- The data structure should support a configurable initial threshold that controls when garbage collection is first triggered.
- When the number of tracked entries exceeds this threshold, the structure should automatically scan and remove entries that are no longer active (as determined by whether the ID still exists in the system).
- After garbage collection runs, the reported entry count should reflect only the active entries that remain.
- The threshold should be configurable through the traits interface so users can tune the trade-off between memory efficiency and collection frequency.

## Why This Matters

Without garbage collection, the data structure can grow to contain tens of thousands of stale entries in high-throughput scenarios, wasting memory and slowing down all operations that scan the list. Adding a configurable GC mechanism allows long-running systems to reclaim memory and maintain predictable performance over time.
