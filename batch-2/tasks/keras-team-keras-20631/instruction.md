Implement a more intuitive model export API in Keras by renaming the export function to reflect the output format and adding a convenient export method to model objects. Ensure the API supports input signature specifications and provides clear error messages for unsupported configurations.

*   Rename the export function to `export_saved_model` in `keras/src/export/export_lib.py`.
    *   Accept `model` and `filepath` as required arguments.
    *   Include optional arguments: `input_signature`, `is_static`, and `jax2tf_kwargs`.
    *   Ensure the exported model's `serve()` method produces outputs numerically close to the original model for the same input.

*   Enhance `export_saved_model` to:
    *   Accept `input_signature` as a tuple or list of `InputSpec`, `TensorSpec`, `KerasTensor`, or backend tensor objects.
    *   Raise a `TypeError` with a message containing 'Unsupported x=' for unsupported `input_signature` element types.
    *   Support JAX-specific options: `is_static` (bool) and `jax2tf_kwargs` (dict or None).
    *   Raise a `ValueError` with 'It must be built' if the model is unbuilt.
    *   Raise a `ValueError` with 'It must be called' if a subclassed model is built but not called.

*   Update the `Model` class in `keras/src/models/model.py` to include:
    *   An `export` method with signature `export(self, filepath, format='tf_saved_model')`.
    *   Default export format as "tf_saved_model".
    *   Raise a `ValueError` with 'Unrecognized format=' for unrecognized format strings.
    *   Raise a `NotImplementedError` with 'The export API is only compatible with JAX and TF backends.' for unsupported backends.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.