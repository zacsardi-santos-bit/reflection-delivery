## Description

When querying AresDB through the broker layer, filter expressions on enum-typed columns use human-readable string values. Internally, these strings must be mapped to their integer indices before the query is dispatched to the data nodes. Currently, there is no dedicated way to retrieve the compiled, enum-resolved form of a query from the query context — callers must access the internal query structure directly, which does not reflect the actual integer mapping used at execution time.

## Expected Behavior

- The query context should provide a method to retrieve the fully rewritten query after compilation, where enum string values in filter expressions have been automatically replaced by their corresponding integer indices.
- For example, a filter that compares an enum column against the string "a" (where the enum dictionary maps "a" to index 0) should appear in the rewritten query as a comparison against the integer 0, formatted without decimal points.
- The rewritten query should be returned as a value (not a reference) so the caller can safely inspect it without risk of modifying the compiled query.
- The rewriting should apply to all expression positions in the query: filters, dimensions, measures, join conditions, and supporting expressions.
- The serialized form of the rewritten query should expose the integer-mapped row filters correctly in its JSON representation.

## Additional Context

The HyperLogLog query result processing should also be extended to correctly handle result sets in which each dimension key contains multiple sub-entries (including null values and various integer-keyed entries), as well as empty result sets.

## Why This Matters

Developers inspecting or logging query plans need to see the actual integer-mapped filters that will be sent to data nodes, not just the original human-readable enum strings. Without this capability, debugging enum-related query behavior requires stepping through internal compiler state.
