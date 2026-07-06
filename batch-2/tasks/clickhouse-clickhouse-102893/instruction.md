I'm seeing incorrect query results from a right-side "any match" join when I have multiple OR conditions in the join clause and disable the query plan table-swap optimization.

*   When performing a right-side 'any match' join with multiple OR conditions in the ON clause, the engine must search through all OR condition maps before stopping, not just stop after the first OR condition produces a match. This applies when query plan table-swap optimization is disabled (query_plan_join_swap_table=0).

*   ANY RIGHT JOIN with OR disjuncts must produce the same correct result set whether or not query plan table-swap optimization rewrites the query into an equivalent LEFT JOIN.

*   For right-side and full outer 'any match' joins, when multiple OR conditions are used, a right-table row that is only reachable via a second or later OR condition must still appear in the result set with its matching left-table row.

*   Hash join must produce correct results for ALL join kinds (INNER, LEFT, RIGHT, FULL) and ALL strictnesses (ALL, ANY, SEMI, ANTI, ASOF) with single integer keys, producing exactly the rows indicated by key equality.

*   Hash join must correctly handle nullable key columns: NULL values on either side must not match any row (NULL != NULL), and non-NULL values must match normally.

*   Hash join must correctly handle multi-key (composite) join conditions using AND, matching only when all key columns are equal.

*   Hash join with an additional ON condition (join mask) that is always false must produce no matched rows for INNER joins and treat all left rows as unmatched for LEFT joins.

*   Hash join with a partial ON condition (join mask) that evaluates to true for only some rows must correctly match only those rows that satisfy the additional condition, producing the correct mix of matched and unmatched rows.

*   ANY strictness must return at most one right-table row per left-table row (for INNER and LEFT), and for RIGHT/FULL, each right-table row must appear at most once even if matched via multiple maps.

*   ALL strictness must return all matching right-table rows for each left-table row (including duplicates), correctly replicating left-table row data for each match.

*   SEMI joins (LEFT and RIGHT) must return only the rows from the respective side that have at least one match on the other side.

*   ANTI joins (LEFT and RIGHT) must return only the rows from the respective side that have no match on the other side.

*   Hash join with OR disjuncts (multiple maps) must produce correct results for all join variants: ANY RIGHT, ALL RIGHT, ALL INNER, LEFT SEMI, LEFT ANTI, RIGHT SEMI, RIGHT ANTI, ANY LEFT, ALL FULL.

*   Hash join must produce correct aggregate results on large tables with many duplicate keys, and must correctly handle the max_joined_block_rows setting by truncating output blocks while still producing the correct total row count.

*   Hash join must correctly join nullable keys with non-nullable keys and LowCardinality keys.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.