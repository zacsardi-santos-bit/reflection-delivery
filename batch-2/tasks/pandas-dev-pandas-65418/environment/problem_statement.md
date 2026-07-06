## Description

When using label-based indexing to assign a list of values to only a subset of rows in a **new** DataFrame column, two bugs affect the result depending on the type of values being assigned.

**Bug 1 — String lists produce garbage in unselected rows:**
If the list contains strings and the row selection does not cover every row in the DataFrame, the unselected rows end up containing a single-character garbage string instead of a proper missing value (NaN). This appears to be a string truncation artifact.

**Bug 2 — Boolean lists raise a type error:**
If the list contains boolean values and the row selection does not cover every row in the DataFrame, the operation raises a type error. The underlying cause is that the new column is initialized with a floating-point data type (to hold NaN for the unselected rows), and then booleans cannot be placed into it.

## Expected Behavior

- Assigning a list of strings to a partial row selection should leave unselected rows as NaN (not as a garbage string character).
- Assigning a list of booleans to a partial row selection should succeed, with unselected rows holding NaN and the column having object dtype.

## Why This Matters

These bugs make it impossible to reliably build up new DataFrame columns row-by-row or in chunks using label-based partial assignment. Users expecting NaN in unselected rows instead get corrupted data or a runtime error depending on the value type.
