Implement support for existence-check subqueries in the Substrait query plan consumer within DataFusion. Extend the consumer to translate Substrait plans containing these subqueries into valid DataFusion logical plans, ensuring compatibility with TPC-H-style queries.

*   Extend the Substrait plan consumer to handle the existence-check subquery type (SET_PREDICATE with EXISTS operation).
    *   Ensure that converting such a plan no longer fails with a 'not implemented' error.
    *   Return a valid DataFusion logical plan after conversion.
*   Translate correlated EXISTS subqueries into DataFusion logical plans.
    *   Represent the EXISTS check as a proper subquery filter expression wrapping the inner relation.
*   For TPCH Q4 Substrait plan (`query_4.json`):
    *   Ensure `from_substrait_plan` produces a logical plan matching the specified debug representation.
*   For TPCH Q5 Substrait plan (`query_5.json`):
    *   Ensure `from_substrait_plan` produces a logical plan matching the specified debug representation, including projections, sorts, aggregates, filters, joins, and table scans.
*   Do not error for unsupported SetPredicate operation types.
    *   Produce an appropriate error message indicating the unsupported type.
*   Ensure the Substrait plan files for TPCH Q4 and Q5 are located at:
    *   `datafusion/substrait/tests/testdata/tpch_substrait_plans/query_4.json`
    *   `datafusion/substrait/tests/testdata/tpch_substrait_plans/query_5.json`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.