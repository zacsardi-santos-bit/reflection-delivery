Implement a new rule in the modeling structure checker to detect unsafe attribute accesses in models using pipeline parallelism. Ensure the rule identifies non-standard attribute accesses on submodules managed by the pipeline parallelism plan and produces a violation message naming the unsafe access expression. Allow suppression of violations with a specific inline comment.

Requirements:
*   Define a constant `TRF011` in `utils/check_modeling_structure.py` as a module-level string, accessible as `cms.TRF011`.
*   Define a module-level variable `_PP_PLAN_MODULES_BY_MODEL_DIR` in `utils/check_modeling_structure.py`, initialized to `None`. This variable should map model directory names to sets of PP-managed submodule names when populated.
*   Update the `analyze_file` function to support `TRF011` via the `enabled_rules` parameter.
*   When `TRF011` is enabled:
    *   Extract the model directory name from the file path and look it up in `_PP_PLAN_MODULES_BY_MODEL_DIR`.
    *   If the directory is not present or the dictionary is empty, produce zero violations for that file.
*   Scan `forward()` methods of classes inheriting from `PreTrainedModel`:
    *   For loop variables iterating over a PP-managed submodule, produce a violation for any non-standard `nn.Module` attribute access. Include the attribute access expression and the full self-reference to the iterated module in the violation message.
    *   For direct attribute access of the form `self.<pp_module>.<attr>`, produce a violation if `<attr>` is non-standard and `<pp_module>` is in the PP plan. Include the full access chain in the violation message.
*   Do not produce `TRF011` violations for standard `nn.Module` attributes (e.g., 'training') accessed on PP-managed submodules or their loop variables.
*   Accessing `self.config.<anything>` must not produce `TRF011` violations.
*   Suppress `TRF011` violations with the comment `# trf-ignore: TRF011` placed on the line immediately before the flagged access.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.