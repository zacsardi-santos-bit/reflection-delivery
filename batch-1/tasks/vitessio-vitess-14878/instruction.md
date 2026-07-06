Implement a new Go package to encapsulate MySQL server capability constants and types, and update the schema comparison library to evaluate instant DDL eligibility for ALTER TABLE operations. Ensure that all references to these capabilities across the codebase point to the new package.

*   Create a new package at `go/mysql/capabilities/`:
    *   Define `FlavorCapability` as an integer type.
    *   Define `CapableOf` as a function type: `func(capability FlavorCapability) (bool, error)`.
    *   Define the following `FlavorCapability` constants using `iota`:
        *   `NoneFlavorCapability` (0)
        *   `FastDropTableFlavorCapability`
        *   `TransactionalGtidExecutedFlavorCapability`
        *   `InstantDDLFlavorCapability`
        *   `InstantAddLastColumnFlavorCapability`
        *   `InstantAddDropVirtualColumnFlavorCapability`
        *   `InstantAddDropColumnFlavorCapability`
        *   `InstantChangeColumnDefaultFlavorCapability`
        *   `InstantExpandEnumCapability`
        *   `MySQLJSONFlavorCapability`
        *   `MySQLUpgradeInServerFlavorCapability`
        *   `DynamicRedoLogCapacityFlavorCapability`
        *   `DisableRedoLogFlavorCapability`
        *   `CheckConstraintsCapability`
        *   `PerformanceSchemaDataLocksTableCapability`

*   Update all existing references in the codebase:
    *   Replace `mysql.FlavorCapability` with `capabilities.FlavorCapability`.
    *   Replace `mysql.CapableOf` with `capabilities.CapableOf`.
    *   Replace all `mysql.*FlavorCapability` and `mysql.*Capability` constants with `capabilities.*Capability`.

*   Implement `AlterTableCapableOfInstantDDL` in `go/vt/schemadiff/capability.go`:
    *   Return `false` if `capableOf` is `nil` or the server lacks `InstantDDLFlavorCapability`.
    *   Return `true` for adding a column at the last position if supported by `InstantAddLastColumnFlavorCapability`.
    *   Return `true` for adding a column at a non-last position if supported by `InstantAddDropColumnFlavorCapability`.
    *   Return `true` for dropping virtual columns if supported, and for non-virtual columns from non-COMPRESSED tables if supported.
    *   Return `false` for any drop-column operation on a table with `ROW_FORMAT` as `COMPRESSED`.
    *   Ensure all ALTER options qualify individually; return `false` if any do not.
    *   Return `true` for MODIFY COLUMN operations changing only the DEFAULT value if supported by `InstantChangeColumnDefaultFlavorCapability`.
    *   Return `true` for extending ENUM or SET columns without crossing storage constraints.
    *   Return `false` for inserting ENUM/SET values in the middle or changing existing values.

*   Implement `CapableOfInstantDDL` method in `go/vt/schemadiff/schema_diff.go`:
    *   Return `true` if all diffs are eligible for instant DDL or are trivially instantaneous operations.
    *   Return `false` if `capableOf` is `nil`.
    *   Accumulate errors using `errors.Join`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.