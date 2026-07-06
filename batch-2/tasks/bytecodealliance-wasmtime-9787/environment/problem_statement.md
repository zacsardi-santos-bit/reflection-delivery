## Description

The Winch compiler backend for AArch64 is missing support for integer-to-float conversion instructions. WebAssembly modules that convert integers to floating-point values (both signed and unsigned, for all combinations of 32-bit and 64-bit types) currently fail to compile or produce incorrect output when targeting AArch64.

Additionally, the existing operations that reinterpret raw bit patterns between integer and floating-point types are currently emitting incorrect instructions. These operations should preserve bit patterns without any numeric transformation, but the current implementation uses arithmetic conversion instructions, which perform a mathematical conversion rather than a raw bit reinterpretation. This produces semantically wrong results.

## Expected Behavior

- Signed integer-to-float conversions should be supported for all combinations: 32-bit and 64-bit integers converting to both 32-bit and 64-bit floats.
- Unsigned integer-to-float conversions should likewise be supported for all size combinations.
- Reinterpret operations (treating the raw bits of an integer as a float, and vice versa) should use bit-preserving move instructions — not arithmetic conversion instructions.
- All of the above should work correctly whether the operand comes from a compile-time constant, a local variable, a function parameter, or a value spilled to the stack.

## Why This Matters

Without these conversions, any WebAssembly module performing numeric type conversions involving integers and floats will fail or behave incorrectly on AArch64. The reinterpret bug also means that bit-level operations (commonly used in low-level numeric code) produce wrong values, silently corrupting results.
