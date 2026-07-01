## Description

Daft currently supports list and struct column types, but there are no expression-level operations to access individual elements within them. Users have no way to extract a specific element from a list column by index, or to pull out a named field from a struct column. These are fundamental operations when working with nested data.

## Expected Behavior

- Users should be able to retrieve an element from a list column by supplying a constant integer index or another column as the index. Both positive and negative indices should be supported (negative counting from the end).
- When an index is out of bounds or the list row is null, a configurable fallback value should be returned instead of raising an error. If no fallback is given, null is returned.
- If the element at the specified index is itself null (but the list is valid and the index is in bounds), null should be returned — not the fallback value.
- This indexing behavior should work for both variable-length list columns and fixed-size list columns.
- Attempting to use list element access on a column that is not a list type should raise a clear error.
- Users should be able to extract a named field from a struct column as a standalone expression. The result should carry the field's original name and type.
- Null struct rows should produce null in the extracted field result.
- Accessing a field name that does not exist in the struct, or using struct field access on a non-struct column, should raise a clear error.

## Why This Matters

Without these operations, working with nested data in Daft requires exploding or converting to Python, which is expensive and cumbersome. Direct element access and struct field extraction are essential for efficient nested data processing pipelines.
