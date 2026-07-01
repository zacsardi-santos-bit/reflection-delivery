## Description

The shader compiler currently only accepts signed integers as the mip level argument when loading from a mipmapped texture. According to the shading language specification, this operation should also accept unsigned integers. This means developers who write shaders using an unsigned integer value for the mip level will encounter compilation failures or incorrect output, even though such usage is valid by the spec.

## Expected Behavior

- When a shader uses an unsigned integer to specify the mip level in a texture load operation, the compiler should accept it without errors.
- The compiler should correctly translate such shaders to all supported output formats (GLSL, HLSL, MSL, WGSL, and SPIR-V).
- Each backend should produce the appropriate type conversion for the unsigned mip level according to that backend's conventions.

## Why This Matters

Shader authors should be able to use both signed and unsigned integer types interchangeably when specifying mip levels, as the shading language specification does not restrict this to signed integers only. Forcing signed integers creates unnecessary friction and makes certain shader patterns impossible or more cumbersome to express.
