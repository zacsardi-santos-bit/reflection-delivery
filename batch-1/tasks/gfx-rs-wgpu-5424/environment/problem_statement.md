## Description

The naga shader compiler supports several built-in functions for packing and unpacking data values, such as converting normalized floating-point components into packed integer representations. However, it is missing support for raw 8-bit integer packing and unpacking. Specifically, there are no built-in functions to pack a four-component integer vector into a single 32-bit word by taking the low 8 bits of each component, or to reverse that process and extract each byte back into a four-component integer vector.

This is a gap compared to the full WGSL specification, which defines these operations. GPU shader authors who need to efficiently store or transmit compact integer data cannot currently rely on the shader language to perform this common bit-packing operation.

## Expected Behavior

- A new built-in that accepts a four-component signed integer vector and packs the low 8 bits of each component into a single 32-bit unsigned integer in little-endian byte order.
- A corresponding built-in for four-component unsigned integer vectors.
- Two complementary unpacking built-ins: one that extracts bytes with sign extension (returning a signed integer vector) and one without sign extension (returning an unsigned integer vector).
- All four operations must be supported across every backend the compiler targets: GLSL, HLSL, MSL, SPIR-V, and WGSL output.

## Why This Matters

Without these operations, developers must manually emulate byte packing using multiple shader instructions, which is verbose and error-prone. Having first-class language support ensures that shader code is portable across all backends and that each backend generates efficient, idiomatic code for the operation. The signed unpack variant in particular requires correct sign extension from 8-bit to 32-bit, which is easy to get wrong without a built-in.
