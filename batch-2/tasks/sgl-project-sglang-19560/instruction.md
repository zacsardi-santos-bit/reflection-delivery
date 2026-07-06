I'm working with a tensor comparison utility used for debugging model outputs, and I'd like to improve the statistical information it reports.

*   TensorStats must include a new required field abs_mean (float) representing the mean of the absolute values of the tensor's elements. This field is mandatory — constructing TensorStats without it must fail.

*   TensorStats must replace the four individual optional percentile fields (p1, p5, p95, p99) with a single field percentiles of type dict[int, float] with a default of empty dict. Integer keys are percentile ranks (e.g., 1, 5, 50, 95, 99) and values are the corresponding quantile floats.

*   DiffInfo must include a new field abs_diff_percentiles of type dict[int, float] with a default of empty dict. Integer keys are percentile ranks and values are quantiles of the absolute element-wise difference.

*   _compute_tensor_stats must compute abs_mean as the mean of the absolute values of the input tensor elements.

*   _compute_tensor_stats must compute percentiles as a dict mapping each of the ranks 1, 5, 50, 95, 99 to the corresponding quantile value of the tensor. If the tensor has at least QUANTILE_NUMEL_THRESHOLD elements, percentiles must be an empty dict {} instead.

*   _compute_diff must compute abs_diff_percentiles as a dict mapping each of the ranks 1, 5, 50, 95, 99 to the corresponding quantile of the absolute difference tensor. If the absolute difference tensor has at least QUANTILE_NUMEL_THRESHOLD elements, abs_diff_percentiles must be an empty dict {}.

*   format_comparison must include a line '[abs_mean] X.XXXX vs Y.YYYY (diff: Z.ZZZZ)' (values formatted to 4 decimal places) immediately after the '[mean]' line in the per-tensor statistics section.

*   format_comparison must emit one '[pN] X.XXXX vs Y.YYYY (diff: Z.ZZZZ)' line for each integer key N present in both the baseline and target percentiles dicts, with keys sorted ascending. With DEFAULT_PERCENTILES (1, 5, 50, 95, 99) this produces lines [p1], [p5], [p50], [p95], [p99] in that order.

*   format_comparison must append a '[abs_diff] p1=X.XXXX p5=X.XXXX p50=X.XXXX p95=X.XXXX p99=X.XXXX' line after the 'max_abs_diff happens at coord=...' line when abs_diff_percentiles is non-empty. Values must be formatted to 4 decimal places and keys must be sorted ascending. When abs_diff_percentiles is an empty dict, this line must be omitted entirely.

*   TensorStats must reject extra (unexpected) fields by raising an exception, consistent with the existing strict base class behavior.


*   Interface details: Type: Class
Name: TensorStats
Location: python/sglang/srt/debug_utils/comparator/tensor_comparator/types.py
Description: Pydantic model holding summary statistics for a single tensor. The fields `p1`, `p5`, `p95`, and `p99` (Optional[float]) are removed. Two fields are changed/added: a new required `abs_mean: float` field (mean of absolute element values), and a `percentiles: dict[int, float]` field (default empty dict) replacing the four individual percentile fields. Integer keys in `percentiles` represent percentile ranks (e.g., 1, 5, 50, 95, 99).

Type: Constant
Name: DEFAULT_PERCENTILES
Location: python/sglang/srt/debug_utils/comparator/tensor_comparator/types.py
Description: A tuple of integer percentile ranks to compute: (1, 5, 50, 95, 99). Used by the comparator to know which percentile levels to include in TensorStats.percentiles and DiffInfo.abs_diff_percentiles.

Type: Class
Name: DiffInfo
Location: python/sglang/srt/debug_utils/comparator/tensor_comparator/types.py
Description: Pydantic model holding diff statistics between two tensors. A new field `abs_diff_percentiles: dict[int, float]` (default empty dict) is added. Integer keys represent percentile ranks of the absolute difference distribution.

Type: Function
Name: _compute_tensor_stats
Location: python/sglang/srt/debug_utils/comparator/tensor_comparator/comparator.py
Signature: _compute_tensor_stats(x: torch.Tensor) -> TensorStats
Description: Computes per-tensor statistics. Must now compute `abs_mean` as the mean of absolute element values. Must compute `percentiles` as a dict mapping each rank in DEFAULT_PERCENTILES to its quantile value; for tensors with `numel() >= QUANTILE_NUMEL_THRESHOLD`, must return `percentiles={}`.

Type: Function
Name: _compute_diff
Location: python/sglang/srt/debug_utils/comparator/tensor_comparator/comparator.py
Signature: _compute_diff(x_baseline: torch.Tensor, x_target: torch.Tensor, ...) -> DiffInfo
Description: Computes diff statistics between two tensors. Must now compute `abs_diff_percentiles` as a dict mapping each rank in DEFAULT_PERCENTILES to its quantile value of the absolute difference tensor; for tensors with `numel() >= QUANTILE_NUMEL_THRESHOLD`, must return `abs_diff_percentiles={}`.

Type: Function
Name: format_comparison
Location: python/sglang/srt/debug_utils/comparator/tensor_comparator/formatter.py
Signature: format_comparison(info: TensorComparisonInfo) -> str
Description: Formats a tensor comparison result as a human-readable string. The output format changes: (1) an `[abs_mean] X.XXXX vs Y.YYYY (diff: Z.ZZZZ)` line must appear immediately after the `[mean]` line; (2) percentile lines use the format `[pN] X.XXXX vs Y.YYYY (diff: Z.ZZZZ)` for each key N present in both baseline and target percentiles dicts, sorted ascending — so `[p50]` now appears between `[p5]` and `[p95]`; (3) after the `max_abs_diff happens at coord=...` line, if `abs_diff_percentiles` is non-empty, a line `[abs_diff] p1=X.XXXX p5=X.XXXX p50=X.XXXX p95=X.XXXX p99=X.XXXX` is appended (keys sorted ascending, values formatted to 4 decimal places); (4) if `abs_diff_percentiles` is empty, the `[abs_diff]` line is omitted entirely; (5) if both tensors have empty `percentiles`, no `[pN]` lines are emitted.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.