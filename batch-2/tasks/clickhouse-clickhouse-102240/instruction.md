I'm hitting a crash in ClickHouse when querying a view that was created with a different join nullability setting than what my current session uses.

*   When a view containing a LEFT JOIN is created with nullable join columns enabled, and then queried with nullable join columns disabled (along with the analyzer enabled), the query must return the correct result rows without crashing.

*   The expected output for a LEFT JOIN view query filtered on a join column (where one matching row exists) must return that matching row's data: the join key, the column from the left table, and the column from the right table.

*   The query engine must not produce a segmentation fault (SIGSEGV) when filter propagation into a nested view encounters a column type mismatch between the view's stored metadata types and the actual types present in the execution plan at query time.

*   When the analyzer is enabled and filter propagation detects that a propagated filter's expected column type does not match the column type in the current execution plan header, the filter must be safely skipped rather than applied with mismatched types.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.