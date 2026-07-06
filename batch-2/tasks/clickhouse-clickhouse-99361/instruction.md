I'm working with a ClickHouse table that stores map columns, and I've defined text search indexes on both the keys and values of those maps.

*   When a MergeTree table has a text index (with 'array' tokenizer) built on mapValues(map), and a query filters using map['key'] IN (values) where all values are non-empty strings, the index must be applied to skip granules. The EXPLAIN indexes=1 output must show the index name and a reduced granule count.

*   When a MergeTree table has both a text index on mapKeys(map) and a text index on mapValues(map), filtering with map['key'] IN (non_empty_values) must apply both indexes sequentially: the map keys index first reduces granules by checking whether the literal key is present, and the map values index further reduces granules among those remaining.

*   When the IN condition includes the empty string '' — either alone (e.g., map['key'] IN ('')) or mixed with non-empty values (e.g., map['key'] IN ('', 'alpha')) — text indexes on mapKeys(map) and mapValues(map) must NOT be applied to skip granules. The EXPLAIN output must show no index name lines in these cases, only the full granule count (all granules scanned).

*   When map['key'] appears as a component in a tuple IN expression alongside another indexed column — for example (col, map['key']) IN (...) or (map['key'], col) IN (...) — both the text index on the other column and the text index on mapValues(map) must be applied for granule skipping, with each index applied to the granules remaining after the previous index.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.