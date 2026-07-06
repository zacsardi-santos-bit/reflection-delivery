Implement support for four new WGSL built-in functions in the naga shader compiler to handle packing and unpacking of raw 8-bit integer data. Ensure these functions are recognized by the WGSL parser, correctly represented in the intermediate representation, and properly emitted by all code generation backends.

*   Add four new variants to the `MathFunction` enum in `naga/src/lib.rs`:
    *   `Pack4xI8`
    *   `Pack4xU8`
    *   `Unpack4xI8`
    *   `Unpack4xU8`
*   Update the WGSL frontend parser in `naga/src/front/wgsl/parse/conv.rs`:
    *   Extend `map_standard_fun` to map "pack4xI8", "pack4xU8", "unpack4xI8", "unpack4xU8" to the corresponding `MathFunction` variants.
*   Modify type resolution in `naga/src/proc/typifier.rs`:
    *   Ensure `Pack4xI8` and `Pack4xU8` resolve to `TypeResolution::Value(Ti::Scalar(crate::Scalar::U32))`.
    *   Ensure `Unpack4xI8` resolves to `Ti::Vector { size: VectorSize::Quad, scalar: Scalar::I32 }`.
    *   Ensure `Unpack4xU8` resolves to `Ti::Vector { size: VectorSize::Quad, scalar: Scalar::U32 }`.
*   Update argument count handling in `naga/src/proc/mod.rs`:
    *   Ensure `MathFunction::arg_count()` returns 1 for all four new variants.
*   Modify expression validation in `naga/src/valid/expression.rs`:
    *   Validate that `Pack4xI8` receives a `vec4<i32>`, `Pack4xU8` receives a `vec4<u32>`, and `Unpack4xI8`/`Unpack4xU8` receive a `u32`.
*   Update GLSL backend in `naga/src/back/glsl/mod.rs`:
    *   Emit `pack4xI8`/`pack4xU8` as bitwise-AND-mask-and-shift expressions.
    *   Emit `unpack4xI8`/`unpack4xU8` using `bitfieldExtract`.
*   Update HLSL backend in `naga/src/back/hlsl/writer.rs`:
    *   Emit `pack4xI8`/`pack4xU8` with bitwise-AND and shifts.
    *   Emit `unpack4xI8`/`unpack4xU8` using integer constructors with shifts and sign normalization.
*   Update MSL backend in `naga/src/back/msl/writer.rs`:
    *   Emit `pack4xI8`/`pack4xU8` and `unpack4xI8`/`unpack4xU8` similarly to HLSL.
*   Update SPIR-V backend in `naga/src/back/spv/block.rs`:
    *   Implement `pack4xI8`/`pack4xU8` using `OpBitFieldInsert`.
    *   Implement `unpack4xI8`/`unpack4xU8` using `OpBitFieldSExtract`/`OpBitFieldUExtract`.
*   Update WGSL output backend in `naga/src/back/wgsl/writer.rs`:
    *   Map functions to emit as "pack4xI8", "pack4xU8", "unpack4xI8", "unpack4xU8".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.