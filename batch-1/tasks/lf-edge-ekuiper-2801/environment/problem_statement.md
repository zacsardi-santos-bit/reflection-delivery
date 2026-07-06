## Description

When building streaming pipelines that process time-range events (e.g., sensor readings or data batches with a start and end timestamp), the same time span can be reported multiple times by different upstream rules or sources. Currently there is no built-in processing node to track which portions of a time range have already been handled, so downstream sinks receive redundant data covering intervals that were already processed.

We need a deduplication trigger node that:
- Keeps a running history of all time intervals it has already processed
- When a new interval arrives, emits only the portions of that interval **not** yet covered by the history
- Automatically expires old history entries beyond a configurable time window to bound memory usage
- Processes events in order of their interval end time (not arrival order), so out-of-order data is handled correctly

## Expected Behavior

- When an interval arrives that is completely new, the full interval is emitted as a single new range
- When an interval partially overlaps with previously seen intervals, only the uncovered sub-intervals are emitted
- When an interval is fully covered by history, nothing is emitted
- Old history intervals that fall outside the expiration window are pruned on each invocation
- Incoming intervals whose entire span falls outside the expiration window produce no output
- Results are emitted in ascending order of interval end time, regardless of the order events arrive

## Why This Matters

Without this node, pipelines that aggregate overlapping time-range events must implement their own deduplication logic. The new node enables correct, memory-bounded deduplication of time intervals as a first-class streaming operation, reducing redundant processing and simplifying pipeline definitions.
