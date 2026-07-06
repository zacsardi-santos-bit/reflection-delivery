## Description

The current metadata index infrastructure uses "functional index"-specific class names and API methods, even though the same infrastructure needs to serve multiple different index types (including a new secondary index type). This naming is now too narrow and causes confusion as more index types are added.

We need to generalize the index metadata model by renaming the functional-index-specific classes to generic index classes, and renaming the corresponding metadata client accessor to a unified method that works for all index types. At the same time, we need to add support for a new secondary index partition type alongside the existing functional index type.

## Expected Behavior

- The index definition model class and its metadata container class should use generic names rather than "functional index"-specific ones.
- The metadata client should expose a single, generalized method for accessing index definitions, usable for any index type.
- A new secondary index partition type should be recognized by the metadata partition infrastructure.
- A utility method should be available to resolve any metadata partition path string back to its corresponding partition type, throwing a clear error for unrecognized paths.
- Both functional indexes and secondary indexes should behave consistently when determining which partitions are enabled — neither should appear as an independently enabled partition before initialization.

## Why This Matters

As the system grows to support more index types beyond the original functional index, having type-specific names in the shared model creates unnecessary coupling. Generalizing the model and its accessor now allows the same infrastructure to be reused cleanly for secondary indexes and any future index types without additional refactoring.
