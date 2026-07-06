## Description

The stream map type panics with an arithmetic overflow when computing its combined size hint if any of its contained streams report very large (or maximum) remaining item counts.

## Expected Behavior

- When a stream map holds multiple streams that each advertise an extremely large number of remaining items, calling the size hint method should return a saturated lower bound (capped at the maximum representable value) rather than panicking.
- The upper bound of the combined size hint should become "unknown" (unbounded) whenever summing the individual upper bounds would overflow, instead of causing a crash.
- In general, computing the combined size hint of a stream map must never panic due to integer overflow, regardless of what the individual streams report.

## Why This Matters

Code that manages a collection of streams should be robust even when those streams advertise extreme or pathological size estimates. Currently, inserting two or more streams that both report the maximum possible size causes an immediate panic on the next call to get the size hint, which is an unexpected crash in otherwise normal usage. This should be a saturating/safe operation instead.
