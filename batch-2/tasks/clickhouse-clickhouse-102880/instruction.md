I have a table with a text index configured so that each row's column value is stored as a single atomic token in the index dictionary — rather than being split into individual words.

*   When `use_text_index_like_evaluation_by_dictionary_scan` is enabled and a table has a text index with `tokenizer = array`, LIKE queries (case-sensitive wildcard patterns of the form `%substring%`) must produce exactly the same result rows as when the setting is disabled.

*   When `use_text_index_like_evaluation_by_dictionary_scan` is enabled and a table has a text index with `tokenizer = array`, NOT LIKE queries must produce exactly the same result rows as when the setting is disabled.

*   When `use_text_index_like_evaluation_by_dictionary_scan` is enabled and a table has a text index with `tokenizer = array`, ILIKE queries (case-insensitive wildcard patterns) must produce exactly the same result rows as when the setting is disabled, including patterns given in all-uppercase.

*   When `use_text_index_like_evaluation_by_dictionary_scan` is enabled and a table has a text index with `tokenizer = array`, NOT ILIKE queries must produce exactly the same result rows as when the setting is disabled.

*   With `use_text_index_like_evaluation_by_dictionary_scan` enabled and a text index using `tokenizer = array`, a LIKE pattern that matches no token in the index dictionary must result in 0 parts and 0 granules being read (as reported by EXPLAIN indexes=1).

*   With `use_text_index_like_evaluation_by_dictionary_scan` enabled and a text index using `tokenizer = array`, a LIKE pattern that case-sensitively matches a subset of index tokens must limit the scanned parts and granules to only those containing matching tokens.

*   With `use_text_index_like_evaluation_by_dictionary_scan` enabled and a text index using `tokenizer = array`, an ILIKE pattern that matches no token (even case-insensitively) must result in 0 parts and 0 granules scanned.

*   With `use_text_index_like_evaluation_by_dictionary_scan` enabled and a text index using `tokenizer = array`, an ILIKE pattern that case-insensitively matches a subset of tokens must limit the parts and granules scanned to those containing matching tokens, and an uppercase version of the same pattern must yield identical part/granule counts.

*   The EXPLAIN output for queries using a text index with `tokenizer = array` must include a line with `Description: text GRANULARITY 100000000` when the index is defined with GRANULARITY 1 and `index_granularity = 1`.


*   Interface details: The tests exercise purely SQL-level behavior through ClickHouse query execution. No new public functions or classes are added — the change is an extension of existing internal logic.

The following SQL-level identifiers are used by the tests and must exist exactly as named:

**ClickHouse Setting:**
- Name: `use_text_index_like_evaluation_by_dictionary_scan`
- Type: Boolean setting (0 = disabled, 1 = enabled)
- Controls whether the text index dictionary is scanned to evaluate LIKE/ILIKE predicates

**Text Index Type (DDL):**
- Syntax: `INDEX <name>(<column>) TYPE text(tokenizer = array)`
- The `tokenizer = array` parameter specifies the whole-value tokenizer mode
- The LIKE/ILIKE dictionary-scan optimization must be extended to support this tokenizer type, so that enabling `use_text_index_like_evaluation_by_dictionary_scan` also works for indexes using `tokenizer = array`

**Primary implementation file:**
- `src/Storages/MergeTree/MergeTreeIndexConditionText.cpp` — contains the LIKE/ILIKE optimization logic that must be updated to support the array tokenizer


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.