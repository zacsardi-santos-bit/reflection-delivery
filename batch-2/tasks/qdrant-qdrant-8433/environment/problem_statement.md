## Description

When filtering points using a field index, the filtering operation currently returns either a matching iterator or nothing. There is no mechanism to signal that an error occurred during the filtering process. This means errors are silently discarded and callers cannot distinguish between "this filter type doesn't apply to this index" and "something went wrong internally."

We need to add proper error propagation to the field index filtering interface so that the result type can express both "not applicable" (no match for this index type) and "an error occurred."

## Expected Behavior

- The filtering operation on a field index should be able to signal errors to callers rather than silently suppressing them.
- The return value should carry both an error layer (to propagate failures) and an optional layer (to indicate whether the filter condition applies to the given index type).
- Callers should handle both the error case and the "not applicable" case separately when consuming filter results.

## Why This Matters

Without error propagation at the filtering layer, genuine failures during index filtering are invisible to callers, making debugging difficult and preventing the system from recovering gracefully from errors. This change lays the groundwork for robust error handling throughout the entire filtering pipeline.
