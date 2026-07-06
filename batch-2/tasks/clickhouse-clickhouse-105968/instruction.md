I'm hitting a server crash in ClickHouse when using the "single value or null" aggregate function with the semi-structured document column type.

*   The singleValueOrNull aggregate function must work correctly with the JSON type. When all rows have the same JSON value, the function must return that JSON value. When rows contain two or more distinct JSON values, the function must return NULL.

*   Creating a MergeTree table with an AggregateFunction(singleValueOrNull, JSON) column must succeed, and inserting aggregate states via the singleValueOrNullState combinator must work without error.

*   The singleValueOrNullMerge function must not crash (no segmentation fault or server error) when deserializing aggregate states for JSON type from a MergeTree table. After deserialization, the merged result must return NULL for each row (due to a known pre-existing limitation where the serialize/read path does not persist the internal state flags).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.