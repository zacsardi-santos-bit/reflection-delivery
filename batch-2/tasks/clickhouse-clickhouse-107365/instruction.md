I'm running into a bug with the sorting-prefix fill feature in ClickHouse.

*   The session setting 'use_with_fill_by_sorting_prefix' must be supported and, when set to 1, enable grouping by sorting prefix in WITH FILL queries.

*   When ORDER BY specifies a collation on the sorting-prefix column and WITH FILL is used, strings that are equal under that collation must form a single fill group instead of being treated as separate groups.

*   Given input rows ('a',1), ('A',2), ('b',1), ('b',3) ordered by the string column ASC with a case-insensitive collation and WITH FILL FROM 1 TO 4, the result must be exactly 6 rows: (a,1), (A,2), (A,3), (b,1), (b,2), (b,3). The collation-equal 'a'/'A' spellings must not be filled separately over the whole domain.

*   The collation-aware fill group detection must produce consistent results regardless of dataset size: a long run of more than 16 rows (which triggers the binary-search range-end path) and a short run of fewer than 16 rows (which uses the linear-probe path) must produce identical groupings for the same collation-equal data shapes.

*   Fill group boundaries must be correctly tracked across chunk boundaries: when collation-equal values are interleaved across many blocks (e.g., 40,000 rows with alternating 'a'/'A' and max_block_size=16384), no spurious fill rows must be inserted, and the output row count must equal the input row count.

*   Collation-aware fill grouping must work for both ASC and DESC orderings. With DESC collation ordering, given the same 4-row input, the 6-row output must list the groups in reverse collation order.

*   The fix must be applied in src/Processors/Transforms/FillingTransform.cpp, where sort-prefix equality comparisons must use the collation associated with each sort description entry (via compareAtWithCollation) when a collator is present, falling back to plain compareAt when no collator is set.


*   Interface details: NO INTERFACES NEEDED

The tests are SQL-level stateless query tests that run against a ClickHouse server and compare output against a reference file. They do not import or call any specific C++ function, class, or method by name. The required change is a behavioral fix inside `src/Processors/Transforms/FillingTransform.cpp` — specifically, the sort-prefix equality comparisons used during fill group detection must be made collation-aware. No new public API, function name, or class name is required by the tests.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.