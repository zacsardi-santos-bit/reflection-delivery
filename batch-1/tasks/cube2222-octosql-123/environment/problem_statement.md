## Description

The query optimizer's filter pushdown logic incorrectly merges filter conditions into data source scans even when those conditions are not pure column comparisons. Specifically, when one side of a filter predicate is a function applied to a data source column — rather than a direct column reference — the optimizer still tries to push the filter down to the data source. Data sources can only evaluate filters against direct column references; they cannot handle arbitrary derived expressions. This produces incorrect query plans when the filter should have been kept as a separate evaluation layer.

## Expected Behavior

- A filter predicate where one side is a complex expression (such as a function call) over a data source column and the other side is a column from a different source should remain as a separate filter, not be merged into the data source.
- A filter predicate where one side is a complex expression over a data source column and the other side is also a column from that same data source should remain as a separate filter.
- A filter predicate where one side is a complex expression whose inputs come entirely from other sources, and the other side is a direct column reference from the data source, should still be correctly pushed down into the data source.

## Why This Matters

Incorrect filter pushdown can cause queries to silently produce wrong results or fail at execution time because the data source receives predicates it cannot evaluate. The optimizer must strictly distinguish between direct column references (which data sources can handle) and derived expressions over those columns (which they generally cannot).
