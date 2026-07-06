I'm hitting a data-correctness bug with a low-cardinality string column after a merge.

*   When a MergeTree table has a LowCardinality(String) column and a merge operation produces multiple dictionaries within a single index granule (because each merge output block overflows the dictionary size limit), the merged part must still store exactly one mark position for the dictionary keys substream — i.e., all dictionary blocks share one mark offset.

*   After the merge described above, querying rows whose values fall in the first dictionary block and rows whose values fall in later dictionary blocks must both return the correct string values — the system must not incorrectly apply only the first dictionary to all rows in the granule.

*   The system must correctly distinguish between a true single-dictionary part (where one dictionary covers all rows and the dictionary stream is exhausted after reading it) and a false-positive case where marks appear uniform but multiple dictionaries were written within a single granule. In the false-positive case, the dictionary must not be cached as a global shared dictionary; instead, dictionary updates must be read from the stream during normal deserialization.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.