## Description

The Winch compiler's AArch64 backend does not currently support four wide arithmetic operations from the WebAssembly wide arithmetic proposal: 128-bit addition, 128-bit subtraction, signed 64×64-to-128-bit multiplication, and unsigned 64×64-to-128-bit multiplication. When a WebAssembly module uses any of these instructions and is compiled with Winch targeting AArch64, the compilation fails with an "unimplemented" error.

These operations are already supported on x86-64 with Winch. The AArch64 backend is missing the corresponding implementation, and additionally the wide arithmetic proposal is currently marked as entirely unsupported for Winch on AArch64 — preventing any module that uses these instructions from being compiled at all.

## Expected Behavior

- The Winch AArch64 backend should compile WebAssembly modules that use 128-bit integer addition, 128-bit integer subtraction, signed wide multiplication, and unsigned wide multiplication.
- These operations should work correctly whether operands come from constant values, local variables, or function parameters.
- The wide arithmetic proposal should no longer be treated as unsupported on AArch64 with Winch.

## Why This Matters

Developers writing WebAssembly modules that perform 128-bit arithmetic (common in cryptography, arbitrary precision arithmetic, and overflow-checked math) cannot use the Winch compiler on AArch64 for these workloads. Bringing AArch64 to parity with x86-64 for these operations is necessary for consistent cross-platform support.
