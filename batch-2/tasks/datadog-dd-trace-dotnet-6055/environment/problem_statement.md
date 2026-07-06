## Description

When test sessions are instrumented and their data is sent to Datadog CI Visibility, the resulting span metrics do not include information about the number of logical processors available on the host machine. This makes it harder to correlate test performance and timing data with the underlying hardware environment.

## Expected Behavior

- Each CI test session span should automatically include the host's logical processor count as a numeric metric under a dedicated logical CPU count metric key.
- The value should equal the number of logical processors reported by the runtime.
- This metric should be read-only — it is set once at session initialization and cannot be overridden by external callers.
- The metric should be serialized correctly as part of the standard metrics payload for CI test session spans.

## Why This Matters

Test execution time can vary significantly depending on how many logical CPUs are available on the CI host. Without capturing this information as part of the test session telemetry, it is difficult to distinguish between performance regressions and environmental differences. Adding the logical CPU count as a standard read-only metric gives teams the context they need to interpret timing data accurately.

## Notes

- The metric tag constant must be added to the shared CI tags file so it can be referenced consistently across the test instrumentation layer and any verification or snapshot tooling.
- The generated serialization code for the session tags class must also be updated to include the new metric in enumeration, get, set, and write operations.
