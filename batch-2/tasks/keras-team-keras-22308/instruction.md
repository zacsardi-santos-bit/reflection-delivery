Implement the `CompileMetrics` class to manage per-output metrics during model compilation, ensuring correct mapping of metric objects to model outputs. Address issues with metrics specified as dictionaries, including handling of nested structures and improving error messages for invalid keys.

*   Implement the `__init__(self, metrics, weighted_metrics, output_names)` method:
    *   Accept `metrics` as a dictionary, `weighted_metrics`, and `output_names` as a list.
    *   Ensure metrics are mapped to outputs positionally when `output_names` match the metrics dictionary keys.
    *   Use the pattern '{output_name}_{metric_name}' for result keys when mapping by `output_names`.

*   Implement the `build(y_true, y_pred) -> None` method:
    *   Support mapping when `output_names` do not match the metrics dictionary keys but metrics keys match `y_true/y_pred` dictionary keys.
    *   Order results to match the sorted output dictionary key order using the pattern '{output_key}_{metric_name}'.

*   Implement the `update_state(y_true, y_pred, sample_weight=None) -> None` method:
    *   Ensure metrics declared in a different order than `output_names` are reordered to match the `output_names` order.
    *   Support deeply nested metrics structures by flattening all metrics into a single result dictionary.
    *   Use the individual metric object's own name attribute for result keys in nested structures.

*   Implement the `result(self) -> dict` method:
    *   Return a dictionary whose keys are in a consistent, deterministic order.
    *   Match the order of `output_names` or the sorted output dictionary key order, depending on the keys used for metric mapping.

*   Raise a `ValueError` with a clear message when a metrics dictionary contains a key that does not correspond to any model output:
    *   Ensure the error message contains the substring 'Invalid `metrics`' and includes the invalid key name.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.