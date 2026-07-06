## Description

An executor assignment field was added to task instances and task/operator definitions in anticipation of a feature that would allow individual tasks to target a specific executor. However, this feature was never completed or supported, and the field was left in place across multiple parts of the system — the task instance data model, the DAG serialization format, and the web UI task views.

## Expected Behavior

- Task instance data structures (as returned by the model layer) should not include an executor assignment field, since per-task executor selection is not a supported capability.
- Serialized DAG and task/operator definitions should not include an executor field.
- Web view responses listing task instances should not expose an executor field in any task instance record.

## Why This Matters

Including an unsupported field in public-facing data structures is confusing for users and developers who may attempt to use it, and creates false expectations. Removing the field entirely is cleaner than keeping a placeholder that does nothing. This aligns the actual data model with the currently supported feature set.
