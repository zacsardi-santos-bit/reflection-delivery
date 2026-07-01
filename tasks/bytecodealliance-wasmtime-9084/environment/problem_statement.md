## Description

There is an inconsistency in the RISC-V 64-bit code generation backend: when converting 32-bit integers to 64-bit double-precision floating-point numbers, the generated instructions are missing the explicit rounding mode specification, unlike all other similar conversion instructions in the backend.

Specifically, when converting either a signed or unsigned 32-bit integer to a 64-bit double, the generated output does not include the rounding mode. All other integer-to-float conversions in the same backend — for example, conversions from 32-bit integers to 32-bit floats, or from 64-bit integers to 64-bit doubles — correctly specify the rounding mode. This inconsistency appears to be a bug where the wrong data width is being passed internally when encoding these two conversion instructions.

## Expected Behavior

- Converting an unsigned 32-bit integer to a 64-bit double should produce a conversion instruction that explicitly includes the round-to-nearest rounding mode.
- Converting a signed 32-bit integer to a 64-bit double should also produce a conversion instruction with the round-to-nearest rounding mode.
- Both of these should be consistent with how all other floating-point conversion instructions are handled in the RISC-V 64-bit backend.

## Why This Matters

Consistency in rounding mode specification across all floating-point conversion instructions is important for correctness. An incorrect or omitted rounding mode can lead to subtly wrong floating-point results on hardware that applies the rounding mode field literally.
