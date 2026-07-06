## Description

The rendering crate needs to be updated to work with the latest major release of the underlying GPU library. The new version introduces several breaking API changes that require updates across multiple files in the renderer.

The most significant change is that GPU device and command queue objects no longer need to be wrapped in reference-counted smart pointers before being passed to the rendering context. Previously, callers had to manually wrap these objects before construction; now ownership can be transferred directly. This simplifies the construction API and removes unnecessary boilerplate.

The new version also renames a family of types used to describe texture and buffer copy operations. Texture copy info, buffer copy info, and buffer layout types are all renamed under a new naming convention throughout the API. There are also smaller changes to how GPU instances are initialized — the descriptor is now passed by reference rather than by value — and a utility function for reading backend preferences from environment variables has been moved to a different location in the API surface.

## Expected Behavior

- The rendering context should accept device and queue objects directly by value, without requiring the caller to wrap them in reference-counted pointers first
- All texture and buffer copy operations throughout the renderer should use the updated type names from the new GPU library version
- GPU instance creation should pass the configuration by reference
- The backend selection utility should use the updated API location
- All existing renderer tests should continue to pass after the upgrade

## Why This Matters

Keeping the GPU library dependency current ensures we benefit from bug fixes, performance improvements, and continued platform support. The API simplification (removing the need for reference-counted wrappers) also makes the code cleaner and easier to reason about.
