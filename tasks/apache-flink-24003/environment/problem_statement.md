## Description

When a Flink job encounters a failure, it's common for multiple tasks to fail nearly simultaneously—for example, when one task's failure cascades to others before the job has had a chance to restart. Currently, each of these concurrent failures is treated as a completely independent restart event. This means:

- The job's restart counter is incremented once per failure, even if all failures are really part of the same event.
- A separate entry is created in the exception history for each concurrent failure, making it hard to see what actually triggered the restart.

Operators and developers diagnosing job stability issues end up with inflated restart counters and a cluttered exception history that doesn't accurately reflect the true number of distinct failure episodes.

## Expected Behavior

- When failures occur while a restart is already pending, they should be treated as concurrent failures belonging to the same restart attempt—not as independent restart events.
- Only the first failure in a new restart attempt should increment the restart counter.
- Concurrent failures should be attached as sub-entries to the root-cause exception history entry rather than creating their own top-level entries.
- It must be possible to attach additional concurrent failures to an existing exception history entry after the initial entry has been created.
- Non-recoverable failures (those that permanently suppress restarts) should always be treated as root-cause failures, regardless of whether a restart was already pending.

## Why This Matters

Accurate restart counters and a clean exception history are essential for operators to understand job health, set appropriate failure thresholds, and diagnose the root cause of instability. Conflating cascading concurrent failures with independent restart events makes this analysis unnecessarily difficult.
