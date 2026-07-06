## Description

When using the Gravitino CLI to inspect table column metadata, the output does not include each column's default value. Users viewing column details can see the name, data type, nullability, auto-increment flag, and comments — but there is no way to see what default value a column is configured with. This information is important for understanding a table's full schema without needing to query the underlying database directly.

## Expected Behavior

- Both the plain (CSV) and formatted table output modes should include a "Default" column when listing column information.
- Numeric default values should appear as plain numbers.
- String default values should appear as their literal text; an empty string default should be visually distinguishable (e.g., shown with surrounding quotes).
- Function-based defaults (such as auto-populating a timestamp) should appear as function call expressions including any arguments.
- Columns with no default value configured should display a blank entry.
- For columns whose type does not support auto-increment (non-integer types), the auto-increment field should be left blank rather than showing a false value.

## Additional Utility

A shared utility should handle display width calculation for CLI table alignment, properly accounting for wide characters (such as Chinese or Japanese text) that occupy two display columns. This utility should also centralize the formatting logic for comments (returning a placeholder when no comment is set) and default values.

## Why This Matters

Without default value visibility, CLI users cannot fully evaluate a table schema at a glance. Adding this column to both output formats makes the CLI a complete tool for schema inspection.
