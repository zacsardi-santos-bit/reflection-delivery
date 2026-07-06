## Description

Daft currently has no built-in way to generate unique row identifiers across a distributed, partitioned dataset. Users who need stable row IDs — for example to join computation results back to the source data, or to track rows across transformations — have no standard mechanism for this.

## Expected Behavior

- A new operation on a DataFrame that generates a column of monotonically increasing unique integer identifiers.
- The default column name should be "id", but users should be able to specify a custom column name.
- The generated ID column should use an unsigned 64-bit integer type.
- The encoding should store the partition number in the upper portion of the 64-bit value and the row's position within its partition in the lower portion. Specifically, the partition number occupies the upper 28 bits (shifted left 36 bits) and the row counter occupies the lower 36 bits.
- The operation must also be supported at the lower-level partition layer, where a partition number and column name are passed explicitly.
- When a single partition contains multiple internal tables, IDs must be assigned sequentially across all of those tables within the partition.
- The operation must handle empty datasets correctly.

## Why This Matters

Without unique row IDs, it is difficult to reliably cross-reference rows from a distributed dataset after transformations, sorting, or joining. Providing a standard, predictable ID generation strategy gives users a reliable way to track rows in large-scale, multi-partition workloads.
