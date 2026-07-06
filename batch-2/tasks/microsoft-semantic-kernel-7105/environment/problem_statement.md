## Description

Objects representing AI function calls in this framework only store a combined name identifier and do not separately expose the plugin name and function name components. This creates friction when constructing these objects from parts, when inspecting individual components after construction, and when serializing the objects for debugging or storage.

Additionally, function call arguments can only be provided as serialized strings. When callers naturally pass native dictionaries, the system fails or behaves unexpectedly. Merging partial streaming function calls with dictionary arguments is also unsupported.

## Expected Behavior

- Function call and function result objects should be constructable using either a combined name string or separate plugin name and function name fields
- Both the plugin name and the function name should be individually accessible on the object after construction
- Both forms should appear in serialized output
- Dictionary arguments should be accepted and properly merged when combining two partial calls
- Attempting to combine incompatible calls (different identifiers, mismatched argument types) should raise a clear error
- When combining streaming message chunks with items that differ in choice index, model identifier, or text encoding, the chunks should be retained as separate items rather than being merged or causing an error

## Why This Matters

Being able to separate and inspect the plugin name and function name makes it easier to route calls, display diagnostics, and build tooling on top of this layer. Supporting dictionary arguments reduces unnecessary serialization overhead and makes the API more ergonomic. Better merging behavior for streaming content prevents subtle data loss or confusing failures.
