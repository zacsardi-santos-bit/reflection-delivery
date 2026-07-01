Implement improvements to the geometry attribute handling in the graphics rendering library. Allow direct use of raw data arrays, align attribute naming with GPU API conventions, and automate attribute layout extraction and completion from shader programs.

*   Update the `Attribute` interface:
    *   Use 'location' instead of 'shaderLocation' for shader binding slots.
    *   Make 'location' an optional property.

*   Modify the `Geometry` class constructor:
    *   Accept plain JavaScript number arrays as attribute values, converting them to `Float32Array`-backed Buffers.
    *   Accept TypedArrays directly, using the same instance as the buffer data.
    *   Accept Buffer instances directly, using the same Buffer instance.

*   Implement the `extractAttributesFromGpuProgram` function in `src/rendering/renderers/gpu/shader/utils/extractAttributesFromGpuProgram.ts`:
    *   Parse WGSL vertex shader source and extract attribute layout data for each `@location`-annotated vertex input in the specified entry point function.
    *   Return a record keyed by attribute name, each containing: location, format, stride, offset (0), instance (false), start (0).

*   Implement the `ensureAttributes` function in `src/rendering/renderers/gl/shader/program/ensureAttributes.ts`:
    *   Accept a `Geometry` instance and a record of `ExtractedAttributeData`.
    *   Fill in any undefined attribute properties (location, format, offset, instance) using the extracted data.
    *   Compute and set stride and start for each attribute:
        *   For interleaved attributes sharing a buffer, calculate combined stride as the sum of all format strides and assign start offsets sequentially.
        *   For non-interleaved attributes, set stride to the attribute's format stride and start to 0.

*   Ensure correct handling of interleaved attributes:
    *   For two interleaved attributes (e.g., `float32x4` and `float32x2`), calculate a combined stride (e.g., 24) and assign start offsets (e.g., 0 and 16).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.