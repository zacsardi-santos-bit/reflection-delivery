I'm seeing a correctness issue with Parquet scans involving floating-point columns.

*   The _skip_batch_predicate method on float column expressions must return a non-None result (indicating batch skipping is possible) for 'less than' comparisons with any literal value, including NaN, because NaN never satisfies a less-than comparison under any ordering.

*   The _skip_batch_predicate method on float column expressions must return a non-None result for 'less than or equal' comparisons with any literal value, including NaN, because NaN never satisfies a less-than-or-equal comparison.

*   The _skip_batch_predicate method on float column expressions must return a non-None result for equality comparisons only when the literal is a non-NaN value; it must return None when the literal is NaN, because Parquet statistics exclude NaN values.

*   The _skip_batch_predicate method on float column expressions must return None for not-equal comparisons regardless of whether the literal is NaN, because hidden NaN values in the data would always satisfy an inequality check.

*   The _skip_batch_predicate method on float column expressions must return None for 'greater than' comparisons with non-NaN literals, because hidden NaN values in the data would always satisfy such a comparison (NaN is treated as the largest value under total ordering). However, 'greater than NaN' must return non-None, because nothing can be greater than NaN.

*   The _skip_batch_predicate method on float column expressions must return None for 'greater than or equal' comparisons with non-NaN literals, because hidden NaN satisfies them. 'Greater than or equal to NaN' must return non-None, because nothing is greater than NaN.

*   When the comparison operands are reversed (literal on the left side, column on the right), the operator direction must be swapped before determining whether a skip batch predicate can be generated for float columns. For example, 'lit(5.0) > col(x)' is equivalent to 'col(x) < 5.0' and must return non-None.

*   The _skip_batch_predicate method on float column expressions for is_between (range) checks must return non-None only when both the lower and upper bounds are non-NaN constants; if either bound is NaN, it must return None.

*   Existing parquet scan tests that previously used 'greater than' filter expressions on float columns must be updated to use 'less than' filter expressions, since 'greater than' on float columns no longer generates skip batch predicates due to NaN safety constraints.


*   Interface details: Type: Method
Name: _skip_batch_predicate
Location: py-polars/polars/expr/expr.py (Python binding; core logic in crates/polars-plan/src/plans/aexpr/predicates/skip_batches.rs)
Signature: _skip_batch_predicate(schema: dict[str, PolarsDataType]) -> Expr | None
Description: Internal method on a polars Expr that attempts to derive a predicate expression usable for skipping row-group batches in Parquet scans, based on stored min/max statistics. Returns None if no safe skip-batch predicate can be derived (e.g., when float NaN semantics would make the optimization incorrect), or returns a new Expr representing the skip condition otherwise. The schema parameter maps column names to their data types (e.g., {"x": pl.Float64()}).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.