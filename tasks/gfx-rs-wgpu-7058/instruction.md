Update the shader compiler to support unsigned integers as the mip level argument in texture load operations. Ensure that the compiler correctly processes shaders using unsigned integers for mip levels and generates appropriate output for all supported backends.

*   Modify the shader compiler to accept unsigned 32-bit integers (u32) as valid mip level arguments for texture load operations.
*   Ensure correct backend-specific handling for unsigned mip levels:
    *   For GLSL output, emit the mip level argument as a double cast (first to the unsigned type, then to int) in the `texelFetch` call.
    *   For HLSL output, emit the mip level argument as a double cast (first to the unsigned type, then to int) in the `texture Load` call.
    *   For MSL output, preserve the mip level argument as an unsigned type using `static_cast<uint>` in the texture read call.
    *   For WGSL output, maintain the mip level argument as `u32`.
    *   For SPIR-V output, ensure the assembly correctly represents the unsigned integer mip level without converting it to a signed integer at the IR level.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.