Implement a method to generate unique row identifiers for a DataFrame that spans multiple partitions in Daft. Ensure the IDs are unique across all partitions using a bit-shifting scheme and allow users to specify a custom column name for the IDs.

*   Implement `MicroPartition.add_monotonically_increasing_id(partition_num, column_name)`:
    *   Return a new `MicroPartition` with an additional column named `column_name` of dtype `UInt64`.
    *   Compute IDs as `(partition_num << 36) + row_offset`, where `row_offset` starts at 0 and increments for each row.
    *   Ensure IDs are sequential across multiple internal tables within a partition.
    *   Handle empty partitions by returning a `MicroPartition` with zero rows and the new ID column in the schema.

*   Implement `DataFrame._add_monotonically_increasing_id(column_name=None)`:
    *   Return a new `DataFrame` with a new ID column prepended. Default column name is 'id' if none is provided.
    *   Ensure the ID column has dtype `UInt64`.
    *   For a single-partition DataFrame (partition number 0), generate IDs as 0, 1, 2, ...
    *   For multiple ordered partitions, assign sequential partition numbers starting from 0 and compute IDs as `(i << 36) | j` for partition `i` and row `j`.
    *   Ensure unique IDs across all partitions for randomly-distributed partitions.

*   Allow `DataFrame._add_monotonically_increasing_id` to accept an optional custom column name, using it instead of 'id' when provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.