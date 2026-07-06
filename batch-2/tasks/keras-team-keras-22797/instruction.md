Implement a feature to export Keras models trained with the PyTorch backend into a PyTorch portable program format. Ensure the exported models are self-contained, loadable with PyTorch utilities, and produce outputs identical to the original Keras models.

*   Update `Model.export()` in `keras/src/models/model.py`:
    *   Accept 'torch' as a valid value for the `format` parameter.
    *   Raise a `ValueError` if the PyTorch backend is not active when `format='torch'`.
    *   Call `export_torch` with the provided `filepath`, `input_signature`, `verbose`, and any additional keyword arguments when `format='torch'`.

*   Implement `export_torch` in `keras/src/export/torch.py`:
    *   Signature: `export_torch(model, filepath, input_signature=None, verbose=None, **kwargs) -> str`
    *   Ensure `filepath` ends with '.pt2'; raise `ValueError` if not.
    *   Use `torch.export.save` to save the model to `filepath`.
    *   Return the `filepath` after successful export.
    *   Forward the following keyword arguments to the underlying torch export call:
        *   `strict` (bool)
        *   `dynamic_shapes` (dict)
        *   `prefer_deferred_runtime_asserts_over_guards` (bool)
        *   `preserve_module_call_signature`
    *   Raise a `ValueError` for unknown keyword arguments with the message: 'Unsupported arguments for `format="torch"`'.
    *   Wrap exceptions from `torch.export.export` in a `RuntimeError` if export fails due to data-dependent control flow with `strict=True`.

*   Ensure the exported model:
    *   Supports sequential, functional, subclassed, multi-input, and multi-output architectures.
    *   Preserves input names in the graph signature for models with named dict inputs.
    *   Handles models using `ops.repeat`, `ops.slice`, `ops.slice_update`, `Conv2D`, `BatchNormalization`, and skip connections correctly.
    *   Produces outputs numerically equal to the original model's outputs with absolute and relative tolerance of 1e-5.

*   Validate input specifications:
    *   Accept an optional `input_signature` as a list of `InputSpec` objects.
    *   Ensure correct output production for inputs matching the signature.

*   Control export behavior:
    *   Accept an optional `verbose` parameter to print informational messages post-export.
    *   Handle `strict` mode to raise `RuntimeError` for models with data-dependent branching.
    *   Support `dynamic_shapes` for varying input sizes along specified dimensions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.