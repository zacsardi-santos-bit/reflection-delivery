Implement support for two new flat sampling qualifiers in the WebGPU shading language (WGSL) frontend, validator, and code generation backends. Ensure shaders using these qualifiers are parsed, validated, and emitted correctly across all supported backends, with specific handling for GLSL.

*   Update the `naga::Sampling` enum in `naga/src/lib.rs` to include:
    *   `First`: Represents the "first vertex" flat sampling qualifier.
    *   `Either`: Represents the "either vertex" flat sampling qualifier.
*   Modify the WGSL frontend to parse `@interpolate(flat, first)` and `@interpolate(flat, either)` as valid, mapping them to `Sampling::First` and `Sampling::Either`.
*   Enhance the validator to:
    *   Accept valid combinations: any interpolation without a sampling qualifier; `perspective` or `linear` with `center`, `centroid`, or `sample`; `flat` with `first` or `either`.
    *   Reject invalid combinations with `VaryingError::InvalidInterpolationSamplingCombination { interpolation, sampling }`.
*   Update the GLSL backend:
    *   Return `Err(naga::back::glsl::Error::FirstSamplingNotSupported)` from `Writer::write()` when encountering `Sampling::First`.
*   Adjust the SPIR-V backend to treat `Sampling::First` and `Sampling::Either` with `Flat` interpolation as plain flat.
*   Modify the MSL backend to treat `Sampling::First` and `Sampling::Either` with `Flat` interpolation as `[[flat]]`, without additional qualifiers.
*   Update the HLSL backend to treat `Sampling::First` and `Sampling::Either` as having no auxiliary qualifier, emitting `nointerpolation` for flat interpolation.
*   Ensure the WGSL backend emits `"first"` for `Sampling::First` and `"either"` for `Sampling::Either` in interpolation attributes.
*   Create a test fixture:
    *   Compile shaders using `flat` and `flat, either` for all backends, including GLSL.
    *   Exclude GLSL for shaders using `flat, first`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.