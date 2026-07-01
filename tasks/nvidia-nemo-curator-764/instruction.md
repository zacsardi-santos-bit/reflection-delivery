Implement a method to create modified copies of processing stages with specified property overrides, while keeping the original stage unchanged. Additionally, enable configuration of individual sub-stages within composite stages by name, applying these configurations sequentially before pipeline execution.

*   Define class-level attributes in `ProcessingStage`:
    *   `_name` (str), `_resources` (Resources), `_batch_size` (int) as backing values for `name`, `resources`, and `batch_size` properties.
*   Implement `ProcessingStage.with_()` method:
    *   Accepts optional keyword arguments: `name` (str or None), `resources` (Resources or None), `batch_size` (int or None), all defaulting to None.
    *   Returns a new instance of the stage with non-None arguments overridden at the instance level.
    *   Preserves original values when None is passed.
    *   Supports method chaining by returning a new instance.
    *   Ensures thread-safety, preventing modification of the original stage or interference between concurrent calls.
    *   Isolates instance-level overrides from subsequent class-level attribute changes.
*   Implement `CompositeStage.__init__()` method:
    *   Initializes `self._with_operations` as an empty list.
*   Implement `CompositeStage.with_()` method:
    *   Accepts a `stage_with_dict` (dict mapping stage names to parameter dicts).
    *   Appends `stage_with_dict` to `self._with_operations` and returns `self`.
*   Implement `CompositeStage._apply_with_()` method:
    *   Accepts a list of `ProcessingStage` instances.
    *   Applies all accumulated `_with_operations` sequentially to the stages list.
    *   Returns the modified list of stages, maintaining the input order.
    *   Raises `ValueError` with "All stages must have unique names" if duplicate stage names exist.
    *   Raises `ValueError` with "Stage {stage_name} not found in composite stage" for non-existent stage names in `_with_operations`.
    *   Returns the original stages list unchanged if `_with_operations` is empty.
*   Implement `CompositeStage.inputs()` method:
    *   Returns inputs of the first stage from the list returned by `decompose()`.
*   Implement `CompositeStage.outputs()` method:
    *   Returns outputs of the last stage from the list returned by `decompose()`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.