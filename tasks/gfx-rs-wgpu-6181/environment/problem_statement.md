## Description

The WebGPU shading language specification allows the flat interpolation mode to carry additional sampling qualifiers that control which vertex's value is used when passing data from vertex to fragment shaders. Specifically, there are two qualifiers that can be combined with flat interpolation: one that mandates the value from the first vertex of a primitive, and one that allows the implementation to freely pick any vertex. These qualifiers are already part of the specification and are accepted by other GPU shading backends, but they are currently not recognized by this implementation — shaders that use them fail to parse.

## Expected Behavior

- The shading language frontend should accept both new flat sampling qualifiers as valid interpolation attributes.
- The validator should enforce that these two new qualifiers are only legal when combined with flat interpolation, and should reject them in combination with perspective or linear interpolation. Conversely, the centroid and sample qualifiers should remain invalid for flat interpolation. Invalid combinations should produce a clear, structured error message identifying the offending interpolation and sampling types.
- The SPIR-V, Metal, HLSL, and WGSL code generation backends should all emit correct output for shaders using the new qualifiers — both qualifiers should be treated as flat with no additional centroid or sample decoration.
- The OpenGL shading language backend does not support the "first vertex" qualifier and should return an explicit error when a shader that uses it is compiled to GLSL.

## Why This Matters

Without this support, shaders targeting WebGPU that rely on these interpolation qualifiers — which are part of the official specification — cannot be compiled or validated at all. Adding support ensures this implementation conforms to the specification and can process real-world shaders.
