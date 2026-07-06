## Description

Our compaction job status store interface currently forces every implementor to independently provide separate implementations for querying all jobs, unfinished jobs, jobs by task, and jobs in a time period. This leads to code duplication and makes it harder to write new implementations — such as an in-memory store for testing — because each query method has to be implemented from scratch.

Additionally, tests that verify the compaction status report screens are tightly coupled to mock objects rather than a realistic status store. This means tests must be programmed with pre-fabricated data, making them brittle and harder to maintain.

## Expected Behavior

- The compaction job status store interface should be refactored so that a single, stream-based method serves as the foundation for all queries. The interface should provide default implementations for all specific query patterns (all jobs, unfinished jobs, jobs by task ID, jobs in a time period) derived from that stream.
- There should be an in-memory implementation of the compaction job status store, usable in tests, that tracks job lifecycle events (created, started, finished) and correctly supports all querying operations.
- The in-memory store should support fixing a clock during testing, so time-sensitive status updates are deterministic.
- The admin client's compaction status report tests should use the in-memory store instead of mocks, exercising the real job lifecycle.

## Why This Matters

This refactoring reduces code duplication in the status store interface and makes it straightforward to create test-friendly implementations. Tests become more realistic because they can record actual lifecycle events and verify the resulting statuses through real queries, rather than programming mock return values for each scenario.
