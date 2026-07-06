Implement the necessary changes in the `CompileMetrics` class to ensure correct metric computation for Keras models with multiple outputs and named outputs. Address issues related to mixed container types, output name ordering, and metric result key prefixing for deeply nested outputs.

*   Modify `CompileMetrics.build(y_true, y_pred)`:
    *   Match the i-th element of `y_pred` (if a flat list) to the dictionary key in `y_true` using `output_names[i]`.

*   Update `CompileMetrics.update_state(y_true, y_pred, sample_weight)`:
    *   Ensure that when `y_pred` is a flat list and `y_true` is a dictionary (or vice versa), the dictionary values are ordered according to `output_names` for correct pairing.
    *   When `y_pred` is a dictionary with keys matching `output_names`, use the `output_names` order to map metrics to outputs and name metric result keys.

*   Implement correct ordering and naming of metrics:
    *   Reorder metrics in a provided metrics dictionary to follow `output_names` ordering.
    *   Ensure result keys appear in `output_names` order, e.g., `output_names=['b','a']` results in keys like `['b_mean_squared_error', 'a_mean_absolute_percentage_error']`.

*   Handle deeply nested output structures:
    *   When `output_names` is provided, prefix each metric result key with the corresponding output name using the format `{output_name}_{metric_name}`.
    *   If `output_names` is `None`, maintain the current behavior with unprefixed metric result keys.

*   Ensure compatibility with outputs of different shapes:
    *   Correctly compute metric values for outputs with varying shapes, such as `output_1` of shape `(3,2)` and `output_2` of shape `(3,3)` or `(3,5)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.