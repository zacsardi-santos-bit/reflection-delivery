## Description

The hstr crate — a high-performance string interning library — currently exists as an external dependency. We want to bring it directly into this repository as an internal crate so it can be developed, tested, and iterated on alongside the rest of the codebase.

The library provides an immutable string type optimized for fast hashing and comparison, and a companion store that manages groups of these strings. The key design properties are:

- Strings created within the same store for the same content share the same internal representation (pointer identity).
- Strings created in different stores for the same content use different internal storage but still compare as equal and hash consistently, making cross-store use in hash-based collections reliable.
- String values remain valid and usable even after the store that created them has been freed.

## Expected Behavior

- Creating two atoms from the same string in the same store should yield atoms that are pointer-identical internally.
- Creating atoms for the same string from two different stores should yield atoms that differ internally (different addresses) but are still semantically equal.
- The same string, regardless of which store it came from, should always produce the same hash value.
- Atoms should remain alive and cloneable after their originating store is dropped.

## Why This Matters

Having the crate in-tree enables easier maintenance, benchmarking, and iteration without coordinating external releases. It also allows the internal string type's behavior to be tested directly within the main repository's CI pipeline.
