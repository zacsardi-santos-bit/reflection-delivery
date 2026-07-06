I've found two bugs when using label-based indexing to assign a list of values to only a subset of rows in a new DataFrame column.

*   When assigning a plain Python list of strings to a partial subset of rows (not covering all rows) in a new DataFrame column via label-based indexing, the unselected rows must receive NaN, not any string value.

*   When assigning a plain Python list of booleans to a partial subset of rows (not covering all rows) in a new DataFrame column via label-based indexing, the assignment must succeed without raising a TypeError, the assigned rows must hold their boolean values, and the unselected rows must receive NaN; the resulting column dtype must be object.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.