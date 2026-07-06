Implement a bitwise left shift operation for integer tensors that respects the tensor's native integer element type. Ensure the operation does not silently convert operands to a different integer type before execution. Update the existing test to skip cases where the integer type's maximum value would lead to overflow.

*   Ensure the bitwise left shift operation uses the tensor's native integer type:
    *   Do not cast operands to any other integer type before performing the shift.
    *   Maintain the declared element type throughout the operation.

*   Update the test for bitwise left shift:
    *   Skip tests for integer types where the maximum value, when cast to u32, is less than 512, to avoid overflow issues.
    *   Ensure tests execute correctly for 16-bit signed, 32-bit signed, 64-bit signed, and 32-bit unsigned integer types, producing results consistent with direct shift computations in their native types.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.