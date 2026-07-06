## Description

When training a model with multiple outputs and specifying metrics as a dictionary, the metric-to-output mapping behaves incorrectly in several scenarios. Users who rely on named outputs with metrics defined as dictionaries encounter silent mapping failures, incorrect metric values being attributed to wrong outputs, and crashes when using nested metric structures.

## Problems

- When a model's internal layer names match the metrics dictionary keys but the actual output variable names are different (e.g., user-facing names), metrics are not correctly mapped to outputs.
- When metrics are declared in a different order than the model's output names, the metrics end up misaligned with outputs instead of being reordered to match the declared output ordering.
- Deeply nested metrics structures (e.g., a dictionary that contains nested dictionaries and lists of metrics) are not supported at all.
- The error message for invalid metric dictionary keys is unclear and unhelpful, making it hard to understand what went wrong.

## Expected Behavior

- When the metric dictionary keys match the model's output names, the metrics should be mapped using those output names positionally (in order), even if the output variables use different names.
- Metrics should always be reordered to match the canonical output ordering, regardless of the order in which they are declared in the metrics dictionary.
- Deeply nested metrics structures should be flattened and each metric should be tracked independently.
- When a metrics key does not correspond to any model output, the error message should clearly indicate the problem and include the invalid key name.

## Why This Matters

Users building multi-output models and specifying per-output metrics via dictionaries cannot trust that their metrics are being tracked correctly. The bug is silent in some cases — metrics are computed but attributed to wrong outputs — making model evaluation unreliable. The fix ensures correct behavior in common real-world model configurations.
