Implement wildcard support for operations on Daft DataFrames to allow concise expressions for selecting, aggregating, and exploding columns. Enable the use of wildcard patterns to reference all columns or expand struct columns into individual fields.

*   Implement wildcard expansion:
    *   Use '*' or `col('*')` in `df.select()` to expand to all top-level columns.
    *   Apply expressions with `col('*')` (e.g., `col('*') * 2`) to each column individually.
    *   Use dot-star patterns (e.g., 'struct_col.*') to flatten struct columns into individual fields.

*   Support wildcard expressions in:
    *   `df.select()`, `df.sum()`, `df.agg()`, and `df.explode()`.
    *   Combine multiple wildcard expressions as separate arguments.

*   Error handling:
    *   Raise `DaftCoreException` with 'cannot have multiple wildcard columns in one expression tree' if two wildcard expressions appear in a single expression tree.
    *   Raise `DaftCoreException` with 'Unsupported wildcard format' for unsupported wildcard syntax.
    *   Raise `DaftCoreException` with 'struct {name} not found' if a dot-star wildcard references a non-existent struct.
    *   Raise `DaftCoreException` with 'struct {path} not found' if a dot-star wildcard references a non-existent nested path.
    *   Raise `DaftCoreException` with 'no column matching {name} is a struct' if a dot-star wildcard targets a non-struct column or path.

*   Column resolution:
    *   Ensure left-associative resolution for dot-star patterns, preferring top-level columns if they are structs.
    *   Treat column names containing '*' as literal references if they exist in the schema.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.