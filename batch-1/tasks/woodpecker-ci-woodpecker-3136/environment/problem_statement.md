## Description

When a pipeline step fails — either by exiting with a non-zero code or being killed due to memory exhaustion — the error message currently includes the step's human-readable name. This is problematic because step names are not guaranteed to be unique across different pipeline runs or parallel executions, making it difficult to correlate errors back to a specific step instance.

## Expected Behavior

- Error messages for steps that exit with a non-zero code should include the step's unique identifier rather than its name, showing the identifier and the exit code value
- Error messages for steps that were killed due to memory exhaustion should include the step's unique identifier rather than its name, along with an indication that an out-of-memory kill occurred

## Why This Matters

Using step names in error messages can be ambiguous when the same step name appears in multiple pipelines or concurrent runs. Switching to unique identifiers makes it much easier to trace an error to the exact step instance in logs, dashboards, and debugging sessions. This improves observability and reduces confusion when diagnosing failures in complex or parallel pipeline setups.
