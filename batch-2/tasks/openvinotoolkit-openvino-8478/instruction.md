Implement the shape inference logic for the `GatherElements` and `SparseReshape` operations in the model optimizer to correctly handle dynamic dimensions. Ensure that the operations raise appropriate errors when encountering incompatible shapes.

*   Update `GatherElements.infer` in `model-optimizer/extensions/ops/gatherelements.py`:
    *   Compute the output shape by using the indices tensor's dimension for the axis dimension. For non-axis dimensions, use the indices tensor's dimension if it is not dynamic; otherwise, use the data tensor's dimension.
    *   Raise `AssertionError` if the data tensor rank does not match the indices tensor rank.
    *   Raise `AssertionError` if the axis value is out of bounds for the tensor rank.
    *   Raise `mo.utils.error.Error` when non-axis dimensions of data and indices are both statically known but have different values.
    *   Use the computed output shape with dynamic dimension handling for setting the output shape, not the raw indices shape.

*   Update `SparseReshape.infer` in `model-optimizer/extensions/ops/sparse_reshape.py`:
    *   Compute the output shape value at `out_port(1)` using the input actual shape (`in_port(1)`) and new shape (`in_port(2)`), supporting dynamic dimensions.
    *   If `new_shape` contains a `-1` and `input_actual_shape` is fully defined with one unknown output dimension, compute the missing dimension as `total_input_elements // product_of_known_output_dims`.
    *   If `new_shape` contains a dynamic dimension and `input_actual_shape` is fully defined with one unknown dimension, resolve it from the total element count.
    *   Maintain dynamic dimensions in the output shape when `input_actual_shape` contains dynamic values and `new_shape` has a `-1` or dynamic entry.
    *   Keep multiple unknown positions in `new_shape` as dynamic dimensions.
    *   Raise `AssertionError` if known total elements of `input_actual_shape` and known elements of the output shape are incompatible, which should be caught by `partial_infer` to raise `mo.utils.error.Error` with 'Stopped shape/value propagation'.
    *   Set the output indices value at `out_port(0)` to the input indices value if input and output shapes are equivalent and index values are available; otherwise, set it to `None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.