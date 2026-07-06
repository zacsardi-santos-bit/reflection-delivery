## Description

The codebase currently keeps a general-purpose set type for tracking object-namespace-relation identifiers in a shared utility package, even though it is only used by the development membership subsystem. This creates an unnecessary cross-package dependency. Additionally, the existing check dispatch logic lacks a dedicated data structure for batching sub-problems by subject type, making it difficult to efficiently group and chunk check requests before dispatching — especially when some relationships carry conditional access (caveats) and some do not.

## Expected Behavior

- A dedicated set type for object-namespace-relation identifiers should be moved into the internal development membership package. It should be a struct-based value type (not a pointer type) with a constructor that accepts optional initial elements. It should support standard set operations: add (returning whether the element was new), membership check, bulk-add from a slice, intersection, subtraction, and union — where union does not mutate the original set but returns a new set. Conversion to a slice should also be supported.
- The membership tracking data structure in that same package should use this local set type for its relationships field, removing its dependency on the shared utility package.
- The shared utility package's implementation and tests for this set type should be removed.
- A new dispatch-grouping structure should be added to the graph package. It should accept relationship tuples and produce chunks grouped by subject type, with each chunk tracking whether any of its members have conditional access. Subjects with and without caveats must be placed in separate chunks. Multiple resources pointing to the same subject must be tracked individually, preserving caveat information per resource, and must be retrievable by subject lookup.

## Why This Matters

Separating subjects with and without caveats into distinct dispatch chunks allows the permission checker to apply the correct evaluation strategy to each group. Without this, conditional and unconditional access cases are conflated. Keeping the set type close to where it is used improves cohesion and removes a dependency that served no architectural purpose.
