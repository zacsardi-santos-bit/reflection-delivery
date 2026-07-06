## Description

Working with Daft DataFrames currently requires listing every column name explicitly whenever you want to apply an operation across columns. This is especially cumbersome when you want to apply the same transformation to all columns in a DataFrame, or when you have struct columns and want to expand their fields without writing out each field individually.

## Expected Behavior

- It should be possible to reference all columns at once using a wildcard expression, so that operations like multiplying every column by a scalar or summing all columns can be expressed concisely.
- A dot-star pattern should be supported to flatten a struct column into its individual fields during a select or aggregation.
- These wildcard expressions should work in all common DataFrame operations: selecting, aggregating, and exploding columns.
- Multiple wildcard expressions targeting different things should be combinable in a single operation.
- Using two wildcard expressions within a single expression tree (e.g., adding a wildcard column to another wildcard column) should produce a clear error.
- Wildcard patterns that don't follow the supported syntax (star not standing alone or not preceded by a dot-separator) should produce a descriptive error indicating the pattern is not recognized.
- Referencing a nonexistent struct via a wildcard should produce a descriptive error naming the missing struct.
- Applying a wildcard to a column that is not a struct should produce a clear error naming the non-struct column.

## Why This Matters

Users working with wide DataFrames or DataFrames with nested struct columns currently have to write verbose, repetitive code that names every column individually. Wildcard support dramatically reduces this boilerplate, making data transformation pipelines more readable and maintainable.
