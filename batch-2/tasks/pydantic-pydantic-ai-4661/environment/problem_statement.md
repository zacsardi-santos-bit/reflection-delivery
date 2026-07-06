## Description

When agent tools signal that their execution should be deferred or requires approval before continuing, the observability/tracing layer currently treats these control-flow signals the same as unexpected errors. This means trace spans for deferred tools get marked as failures, polluting dashboards with false-positive errors and making it impossible to distinguish between actual tool failures and intentional deferral behavior.

## Expected Behavior

- When a tool defers its execution or requests approval, the corresponding trace span should be annotated with:
  - The type of deferral (which kind of deferral was signaled)
  - Any metadata the tool included with the deferral (e.g., a task ID or other context)
- When no metadata is included with the deferral, the metadata annotation should be absent from the span entirely
- When metadata cannot be serialized (e.g., contains custom objects with no standard serializable representation), it should fall back to a string representation
- In newer versions of the instrumentation, a deferred tool span should NOT be flagged as an error — deferral is intentional, not a failure
- In older/legacy instrumentation versions, the existing error-level behavior is preserved for backward compatibility

## Why This Matters

Developers using observability tools to monitor their AI agents need to distinguish between tools that failed unexpectedly and tools that intentionally deferred their execution. Without this distinction, monitoring dashboards show deferred tools as errors, making it difficult to understand agent behavior and diagnose actual problems.
