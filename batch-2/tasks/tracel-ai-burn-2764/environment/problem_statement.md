## Description

Bitwise left shift operations on integer tensors produce incorrect results when the tensor element type is anything other than a specific 32-bit unsigned integer type. The GPU/JIT backend is silently converting all integer operands to that one fixed type before performing the shift, then returning the result in that fixed type rather than the original declared element type. This means 16-bit, 32-bit signed, and 64-bit integer tensors all get the wrong answer from a bitwise left shift.

## Expected Behavior

- A bitwise left shift on a 16-bit integer tensor should produce 16-bit results based on 16-bit shift semantics.
- A bitwise left shift on a 32-bit signed integer tensor should produce 32-bit signed results.
- A bitwise left shift on a 64-bit integer tensor should produce 64-bit results.
- In general, the operation must preserve and use the tensor's declared native integer type throughout, rather than silently upcasting or changing the type.

## Additional Notes

The existing test for bitwise left shift should also be guarded so that it skips for very small integer types (such as 8-bit types) where the test values themselves would overflow the type, avoiding test failures that obscure genuine correctness issues.

## Why This Matters

Bitwise operations are used in numeric algorithms, hash computations, and low-level data manipulation. Getting incorrect results silently because the backend changed the integer type is a serious correctness bug that is difficult to diagnose.
