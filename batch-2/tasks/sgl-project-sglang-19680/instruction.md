I'm working on a tensor comparison debugging utility and need to make a few improvements.

*   The `auto_descend_dir(directory, label)` function must be added to `python/sglang/srt/debug_utils/comparator/utils.py` and exported so it can be imported as `from sglang.srt.debug_utils.comparator.utils import auto_descend_dir`.

*   When `auto_descend_dir` is called with a directory that already contains `.pt` files directly, it must return that directory unchanged.

*   When `auto_descend_dir` is called with a directory that has no `.pt` files at the top level but has exactly one child subdirectory containing `.pt` files (other subdirectories may exist but be empty), it must return that single non-empty child subdirectory.

*   When `auto_descend_dir` successfully descends into a child subdirectory, it must emit a log record whose message contains both the string 'auto-descend' and the label argument (e.g., 'target_path').

*   When `auto_descend_dir` is called with a directory that has no `.pt` files at the top level and two or more child subdirectories each containing `.pt` files, it must raise `ValueError` with a message matching the pattern 'multiple subdirectories contain data'.

*   When `auto_descend_dir` is called with a directory that has no `.pt` files at the top level and no child subdirectories with `.pt` files either, it must raise `ValueError` with a message matching the pattern 'no .pt files found'.

*   The `run` function in `python/sglang/srt/debug_utils/comparator/entrypoint.py` must apply `auto_descend_dir` to both the baseline path (with label `'baseline_path'`) and the target path (with label `'target_path'`) before proceeding with comparison. When a parent directory wrapping a single engine subdirectory is given, the comparison must succeed normally (exit code 0). When auto-descent is triggered for the target path, the emitted log message must contain `'target_path'`.

*   The `parse_dims` function in `python/sglang/srt/debug_utils/comparator/dims.py` must accept dimension strings that use square bracket notation for modifiers on individual dimensions (e.g., `'h[tp:partial] d'`, `'b s[cp] h[tp:partial]'`). It must return an object with a `.dims` attribute that contains the list of parsed dimension specifications.

*   The comparison output formatter must output `max_abs_diff` and `mean_abs_diff` metrics without any pass/fail emoji prefix. Only `rel_diff` retains the emoji prefix (✅ or ❌). The output line format must be: `'✅ rel_diff=VALUE\tmax_abs_diff=VALUE\tmean_abs_diff=VALUE\n'` when rel_diff passes, and `'❌ rel_diff=VALUE\tmax_abs_diff=VALUE\tmean_abs_diff=VALUE\n'` when rel_diff fails.


*   Interface details: Type: Function
Name: auto_descend_dir
Location: python/sglang/srt/debug_utils/comparator/utils.py
Signature: auto_descend_dir(directory: Path, label: str) -> Path
Description: Given a directory path and a label string, returns the directory unchanged if it directly contains .pt files. If it has no .pt files at the top level but exactly one child subdirectory contains .pt files, returns that child subdirectory and emits a log record containing "auto-descend" and the label. Raises ValueError with "multiple subdirectories contain data" if two or more children have .pt files. Raises ValueError with "no .pt files found" if no .pt files are found anywhere.

Type: Function
Name: parse_dims
Location: python/sglang/srt/debug_utils/comparator/dims.py
Signature: parse_dims(dims_str: str) -> object  (result has `.dims` attribute of type list)
Description: Parses a dimension specification string and returns a structured object with a `.dims` attribute containing the list of parsed dimension specifications. Must accept square bracket notation for modifiers on individual dimensions (e.g., "h[tp:partial] d", "b s[cp] h[tp:partial]", "t (num_heads*head_dim)[tp]"). Callers access dimension specs via `.dims` on the returned object.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.