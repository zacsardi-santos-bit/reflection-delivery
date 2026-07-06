Update the query optimizer's filter pushdown logic to correctly handle filter predicates involving complex expressions. Ensure that only direct column references are considered for pushdown into data source scans, while complex expressions remain separate.

*   Implement logic to determine if a filter predicate can be pushed down:
    *   Only allow direct plain variable references as the data-source-local side of a comparison.
    *   Do not consider predicates with function expressions wrapping data source variables as pushdown-able.
*   Ensure filter predicates are handled correctly based on their structure:
    *   If the left side is a function expression with a variable from the data source's alias and the right side is a variable from a different alias, keep the Filter node unchanged.
    *   If both sides reference the same data source but one side is a function expression, do not merge the predicate into the data source.
    *   If a function expression's arguments reference only external aliases and the other side is a direct variable from the data source's alias, allow pushdown if supported by the data source's filter relations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.