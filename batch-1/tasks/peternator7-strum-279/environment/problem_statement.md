## Description

When working with enums, there's often a need to maintain a value associated with each variant — for example, tracking a score, configuration option, or state per variant. Right now, this library offers no derive macro to automatically generate a companion data structure that stores one value per enum variant. Developers have to hand-write verbose match expressions, use hash maps, or maintain parallel arrays — all error-prone and not type-safe.

## Expected Behavior

- A new derive macro should be available, importable from the main crate, that generates a companion table type for any unit-variant enum.
- The generated table type should be generic over the stored value type and should have one slot per non-disabled variant.
- The table should support:
  - Default construction (all slots set to the type's default value)
  - Explicit construction with one value per slot
  - Fill-all construction (all slots set to the same cloned value)
  - Construction from a closure that receives each variant and returns a value
  - Indexed read and write access by variant
  - Cloning the entire table
  - Transforming all slots to a new type using a closure over each variant and its current value
  - Collecting all option-typed slots into a single optional table
  - Collecting all result-typed slots into a single result table, returning the first error encountered
- Variants marked as disabled should be excluded from the table, and attempting to access them via indexing should panic.
- Enum variants whose names conflict with reserved language keywords must compile correctly.

## Why This Matters

This eliminates repetitive boilerplate for a very common Rust pattern: a fixed-size, enum-indexed lookup table. With this macro, developers can safely and concisely associate per-variant data without sacrificing type safety or ergonomics.
