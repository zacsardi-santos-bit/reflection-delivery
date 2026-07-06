Implement a method to retrieve a rewritten form of a compiled query in AresDB's broker layer, where enum string values in filter expressions are replaced by their corresponding integer indices. Ensure the rewritten query is returned as a value type for safe inspection.

*   Update the `QueryContext` type to include a `GetRewrittenQuery` method.
    *   Signature: `func (qc *QueryContext) GetRewrittenQuery() common.AQLQuery`
    *   Location: `broker/query_compiler.go` in the `broker` package.
*   Ensure `GetRewrittenQuery` is called after `Compile()` and returns a copy of the compiled query.
    *   Replace enum string values in filter expressions with integer indices.
    *   Support both `SmallEnum` and `BigEnum` column types.
    *   Format integer indices as whole numbers without decimal points.
*   Include a `rowFilters` array in the JSON representation of the query, containing integer-rewritten filter expressions.
*   Rewrite parsed expressions for measures, dimensions, join conditions, and supporting expressions.
    *   Convert parsed AST nodes back to their string representation if non-nil.
*   Update the `AggQueryPlan` HLL execution path to handle expanded query result sets.
    *   Correctly serialize and deserialize result sets with multiple entries, including null and integer-keyed entries.
    *   Ensure the round-trip preserves all entries exactly.
    *   Handle empty `AQLQueryResult` by returning a result set with one entry equal to an empty `AQLQueryResult`.
*   Modify the expression AST's number literal serialization in `query/expr/ast.go` to format integer number literals as integers, not floats.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.