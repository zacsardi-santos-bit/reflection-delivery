## Description

The IoTeX blockchain storage layer currently relies on a single embedded database engine as the backing store for its key-value store abstraction. We need to add support for a second storage engine — an LSM-tree-based database — as an alternative backend.

## Expected Behavior

- A new constructor function creates a database instance backed by the alternative engine, accepting the same configuration structure used by the existing backend.
- The new backend supports reading a value by namespace and key, returning a typed "not found" error when the key or namespace does not exist.
- Writing a key-value pair into a namespace must work, including overwriting existing values and creating new namespaces on demand.
- Deleting a key must be idempotent: deleting a key that does not exist should succeed silently.
- Atomic batch writes must apply all operations together, with last-write-wins semantics when the same key appears multiple times in one batch.
- A range-filtered scan over a namespace must return only the key-value pairs whose key falls within an inclusive range and satisfies a caller-supplied predicate; if no matching entries exist (or the namespace is empty), it must signal a "not found" condition.
- A full namespace scan must iterate over all entries in a namespace and return no error if the namespace is empty.

## Why This Matters

Different workloads favor different storage engines. Providing an alternative backend gives operators the flexibility to choose the engine with the best performance profile for their deployment without changing any application-level read/write logic.
