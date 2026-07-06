## Description

The task creation page in the Taskcluster UI currently provides no way to validate a task payload against the expected schema for a given worker type before submission. Users can write invalid payloads and only discover problems after submitting the task, which is a poor developer experience.

We need a utility that validates task payloads against their corresponding schemas by fetching those schemas at runtime and checking the payload structure. When validation errors are found, users should see clear, human-readable messages indicating what is wrong and where the problem is in the payload.

## Expected Behavior

- When a task payload is validated against its schema and no errors are found, the result should be an empty list.
- Validation error messages should be enriched with contextual detail: when a field has the wrong type, the path to that field should be included in the message; when an unexpected additional property is encountered, the name of that property should be included in the message.
- When an error has no additional structural detail, the message should be returned as-is.

## Why This Matters

Without inline validation, users submitting tasks with malformed payloads receive no feedback at the UI level. Adding schema validation before submission lets users catch and correct mistakes earlier, reducing frustration and failed task submissions.
