## Description

The Winch baseline compiler for WebAssembly on x86-64 is missing support for SIMD lane extraction operations. When a WebAssembly module uses instructions that read a single scalar element from a 128-bit SIMD vector, the Winch compiler fails rather than generating correct machine code. This affects all element types: 8-bit, 16-bit, 32-bit, and 64-bit integers (both signed and unsigned variants), as well as 32-bit and 64-bit floating-point values.

## Expected Behavior

- The Winch compiler should successfully compile WebAssembly modules that extract individual lanes from SIMD vectors on x86-64 targets with AVX support.
- Signed integer extractions should produce sign-extended results in a 32-bit general-purpose register.
- Unsigned integer extractions should produce zero-extended results without any sign extension.
- Floating-point lane extractions should leave the result in the appropriate floating-point register; for the first lane (index 0), no shuffle is needed; for other lanes, the appropriate shuffle instruction should reposition the value.
- The generated code should use the standard AVX vector extract instructions appropriate to each element size.

## Why This Matters

SIMD lane extraction is a fundamental operation in WebAssembly's SIMD proposal and is used extensively in real-world workloads that process data in vectorized form. Without this support, the Winch compiler cannot compile a large class of SIMD-using modules, significantly limiting the usefulness of the Winch compilation path for these workloads.
