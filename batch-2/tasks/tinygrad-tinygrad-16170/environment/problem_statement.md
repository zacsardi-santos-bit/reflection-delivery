## Description

The masked element selection and non-zero index lookup operations currently always produce outputs whose size depends on how many elements in the input satisfy the condition at runtime. This dynamic output shape makes them incompatible with the JIT compiler, which requires all tensor shapes to be known statically before execution.

## Expected Behavior

Both operations should gain an optional parameter that lets the caller specify a fixed output size up front:

- When the fixed size is larger than the number of matching elements, the extra output slots should be filled with a configurable padding value (defaulting to zero).
- When the fixed size is smaller, the output should be truncated to the specified size.
- The output data type should always match the input tensor's type, even if the provided padding value has a different numeric type (e.g. providing a float fill value for an integer tensor must not change the output to float).
- When a fixed size is specified, both operations should work correctly inside the JIT compiler and produce the right results across repeated compiled invocations with varying inputs.

## Why This Matters

Users who want to use masked selection or non-zero index lookup as part of a compiled execution graph are currently blocked: any attempt to do so raises an error because the dynamic output size requires a runtime value that cannot be resolved at compile time. Adding a fixed-size mode unlocks these operations for use in compiled pipelines, which is critical for performance-sensitive code that is otherwise fully JIT-able.
