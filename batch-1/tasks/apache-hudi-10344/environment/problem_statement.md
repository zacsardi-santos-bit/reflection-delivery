## Description

The spillable map implementation used across Hudi's data processing pipeline has two related issues: it does not support automatic resource cleanup, and it behaves incorrectly when the map is empty.

Currently, when this map spills data to disk, the associated file handles and temporary disk resources are not formally tied to the map's lifecycle. There is no clean way for callers to ensure these resources are always released — especially in error paths — which can lead to resource leaks.

Additionally, if any standard map operation (checking emptiness, querying keys or values, streaming values, iterating) is called on a freshly created map that has never had any entries added, the operation fails because it unconditionally tries to access disk-based storage that hasn't been initialized yet. This makes it impossible to safely use the map in code paths where the map might remain empty.

## Expected Behavior

- The map should support Java's automatic resource management so that callers can guarantee resources are released when done.
- All standard map operations — checking size, emptiness, containment of keys or values, retrieving key sets, value collections, entry sets, streams, and iterators — should work correctly and safely on an empty map without throwing exceptions.
- This should hold true regardless of which disk storage mode is configured.

## Why This Matters

Without these fixes, code using this map is either prone to resource leaks when disk spillage occurs, or is fragile and requires workarounds to avoid crashing when the map has not yet received any data.
