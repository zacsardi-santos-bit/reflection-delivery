Replace the single boolean field in the SQL schema describer library's table data structure with a bitflags field to track multiple table properties. Implement an enum to define these properties and update the JSON serialization to reflect this change. Modify all related methods and database-specific describers to accommodate the new bitflags approach.

Requirements:

*   Update the `Table` struct in `libs/sql-schema-describer/src/lib.rs`:
    *   Replace `is_partition: bool` with `properties: BitFlags<TableProperties>`.
    *   Ensure `properties` has `bits` equal to `0b0` when no flags are set.

*   Implement a `TableProperties` enum:
    *   Use `#[enumflags2::bitflags]` and `#[repr(u8)]`.
    *   Include at least two variants: `IsPartition` and `HasSubclass`.
    *   Ensure the Debug output for an empty `BitFlags<TableProperties>` is `BitFlags<TableProperties> { bits: 0b0 }`.

*   Enable `enumflags2` with the `serde` feature:
    *   Serialize `BitFlags<TableProperties>` to JSON as an integer.
    *   Ensure JSON output shows `"properties": 0` for tables with no special properties.

*   Add and update methods in `libs/sql-schema-describer/src/lib.rs`:
    *   Add `SqlSchema::push_table_with_properties(name: String, namespace_id: NamespaceId, properties: BitFlags<TableProperties>) -> TableId`.
    *   Replace `push_table_partitioned` with `push_table_with_properties` using `BitFlags::from_flag(TableProperties::IsPartition)`.

*   Update `TableWalker` methods in `libs/sql-schema-describer/src/walkers/table.rs`:
    *   Modify `is_partition(self) -> bool` to check the `IsPartition` flag.
    *   Add `has_subclass(self) -> bool` to check the `HasSubclass` flag.

*   Modify all database-specific describers:
    *   Update calls from `push_table_partitioned` to `push_table_with_properties` with appropriate `BitFlags<TableProperties>`.
    *   Ensure regular table calls to `push_table` remain unchanged.

*   Extend PostgreSQL tables query:
    *   Detect and return whether a regular table (`relkind = 'r'`) has subclasses (`relhassubclass`).
    *   Populate the `HasSubclass` flag for applicable tables.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.