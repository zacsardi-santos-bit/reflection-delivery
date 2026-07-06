I have a ClickHouse table with a JSON-typed column and a min/max skip index defined on it.

*   The ColumnObject class in src/Columns/ColumnObject.cpp must implement a getExtremes method with the exact signature: getExtremes(Field & min, Field & max, size_t start, size_t end). The method must compute the true minimum and maximum values within the row range [start, end) rather than always returning the first element or an empty object.

*   When start >= end (empty range), the getExtremes method must set both min and max to empty Object values and return early.

*   The getExtremes implementation must iterate over the range [start, end), comparing elements to find the actual minimum and maximum values, so that a minmax skip index on a JSON column can correctly record per-granule min/max boundaries.

*   When a minmax skip index (TYPE minmax) is defined on a JSON column and a query filters with a greater-than comparison against a JSON literal, the skip index must prune irrelevant granules. For a table with 3 granules containing '{'a':'1'}', '{'a':'2'}', '{'a':'3'}' (one per granule) and a condition j > '{'a':'2'}', only 1 of 3 granules should be read, and the query must return only the rows where j is strictly greater than the compared value.

*   When a minmax skip index is defined on a JSON column and a query filters with an equality comparison against a JSON literal, the skip index must correctly identify matching granules. For the above table with j = '{'a':'2'}', only 1 of 3 granules should be read and the query must return only the matching row.

*   When a minmax skip index is defined on a JSON column and a query filters with a less-than comparison against a JSON literal, the skip index must prune irrelevant granules. For the above table with j < '{'a':'2'}', only 1 of 3 granules should be read, and the query must return only rows strictly less than the compared value.


*   Interface details: Type: Method
Name: getExtremes
Location: src/Columns/ColumnObject.cpp
Signature: getExtremes(Field & min, Field & max, size_t start, size_t end) const
Description: Computes the minimum and maximum values of the ColumnObject within the row range [start, end). When start >= end, sets both min and max to empty Object values and returns. Otherwise iterates over the range to find the actual minimum and maximum elements, storing them in min and max respectively. This method is a member of the ColumnObject class and overrides the base column interface.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.