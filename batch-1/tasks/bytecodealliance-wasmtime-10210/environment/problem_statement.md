# Add SIMD All-True and Bitmask Support to Winch Compiler

## Description

The Winch JIT compiler currently lacks support for two commonly-used families of SIMD reduction operations: checking whether all elements in a packed integer vector are non-zero, and extracting a bitmask from a vector's sign bits. Developers writing or using WebAssembly modules that include these operations cannot compile them using Winch — the compiler either fails or produces incorrect results.

Additionally, when running on hardware that supports advanced vector instruction sets, Winch should emit the more efficient hardware-accelerated instructions rather than generic fallbacks. Currently, even when the required hardware extensions are detected, the compiler does not take advantage of them for these operations.

## Expected Behavior

- The Winch compiler must support the "all lanes non-zero" check for integer vectors of all element widths (8-bit, 16-bit, 32-bit, and 64-bit).
- The Winch compiler must support the bitmask extraction operation for integer vectors of all element widths.
- When hardware support for advanced vector extensions is available, the compiler must emit the corresponding hardware-accelerated instructions for both operation families.
- Correctness must be verified: the all-true check returns 0 when any element is zero, and 1 when all elements are non-zero.
- These operations must work correctly when the vector operand comes from a loaded value or from a splat of a scalar value.

## Why This Matters

WebAssembly's SIMD specification includes these operations as part of the core instruction set. Many real-world workloads (image processing, cryptography, data validation) rely on them. Without Winch support, any module using these operations cannot be run under the Winch compiler, limiting its usefulness as a production JIT backend.
