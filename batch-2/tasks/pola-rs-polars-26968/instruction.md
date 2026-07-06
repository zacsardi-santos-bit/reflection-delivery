I noticed that when I use a windowed expression and accidentally pass the same column name more than once as a partition key, polars just silently accepts it without raising any error.

*   When the `over()` method on a polars expression is called with duplicate partition-by column names (e.g., the same column name appearing more than once in its arguments), it must raise a `pl.exceptions.DuplicateError`.

*   The duplicate-name check must apply regardless of whether the duplicate column names are passed as positional string arguments or within a list.

*   Existing usage of `over()` with non-duplicate partition keys must continue to work correctly without raising any error.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.