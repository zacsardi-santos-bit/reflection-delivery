## Description

The as-of join operation with a "nearest" matching strategy currently does not support a tolerance parameter. This means every left row will always be matched to the nearest right row regardless of how far apart they are, with no way to mark distant matches as missing. For time-series analysis this is a significant limitation: often a match that is too far away in time is worse than no match at all.

Additionally, there is a correctness bug in the nearest strategy: when the last element of the right side is the nearest match for multiple consecutive left-side rows, only the first of those left rows gets matched correctly. Subsequent left rows that should also match the same last right element are not matched correctly due to a distance state issue that persists across left rows.

## Expected Behavior

- Users should be able to specify a maximum allowable distance (tolerance) when using the nearest matching strategy. Right-side rows further than this distance from a left-side key should not produce a match — the result for that row should be null/missing.
- The tolerance should accept numeric values for numeric keys, duration strings for date/time keys, and also time-interval objects for date/time keys (currently only strings are accepted for date/time tolerances).
- The same tolerance support should work when the join is grouped by one or more columns.
- The correctness bug where multiple left rows do not all correctly match the last right element should be fixed.

## Why This Matters

Without tolerance support for the nearest strategy, users doing time-series or event-matching work cannot prevent spurious far-away matches. This forces workarounds like post-filtering, which is error-prone and inefficient. The nearest-with-tolerance pattern is common in financial and scientific data pipelines where events from two streams should only be considered "matching" if they occurred close together in time or value.
