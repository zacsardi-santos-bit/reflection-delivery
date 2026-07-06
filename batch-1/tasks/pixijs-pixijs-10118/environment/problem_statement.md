## Description

Creating geometry objects for rendering is currently more verbose than necessary. Every attribute must be wrapped in a full descriptor object, even when the only information needed is the raw data. Additionally, the property used to bind an attribute to a shader location has a name that doesn't match common GPU API conventions, which is confusing to developers familiar with modern graphics APIs.

There is also no automated way for the rendering pipeline to infer attribute layout from a compiled shader program. Developers must manually provide format, stride, and offset for every attribute, even when this information could be derived automatically from the shader source.

## Expected Behavior

- Geometry attributes should accept raw number arrays, typed arrays, or buffer objects directly — without requiring them to be wrapped in a descriptor object.
- The property that identifies the shader binding slot for an attribute should be renamed to align with standard GPU API naming conventions.
- A utility should be available to extract attribute layout information from a GPU shader program's source code, returning format, stride, offset, location, and related fields for each vertex input.
- A utility should be available that takes a geometry and shader-extracted attribute data and automatically fills in any missing attribute properties, including computing correct stride and start offset values for interleaved attributes that share the same buffer.

## Why This Matters

These changes reduce boilerplate when defining geometry, make the API more consistent with modern GPU APIs, and enable the rendering pipeline to automatically resolve attribute layout details rather than requiring developers to specify everything manually.
