Implement dedicated AST node types for SQL pattern-matching expressions to correctly handle optional escape characters and preserve PostgreSQL-specific operator syntax. Update the SQL parser and generator to support these changes.

*   Parse SQL keywords into dedicated AST variants:
    *   LIKE and NOT LIKE should be parsed into `Expr::Like` with fields: `expr`, `negated`, `pattern`, and `escape_char`.
    *   ILIKE and NOT ILIKE should be parsed into `Expr::ILike` with the same fields.
    *   SIMILAR TO and NOT SIMILAR TO should be parsed into `Expr::SimilarTo` with the same fields.
*   Handle escape characters:
    *   When an ESCAPE clause is present, set `escape_char` to `Some(EscapeChar::escape(c))`.
    *   Without an ESCAPE clause, set `escape_char` to `None`.
*   Define the `EscapeChar` struct:
    *   Implement `escape(ch: char) -> EscapeChar` to create `EscapeChar(Some(ch))`.
    *   Implement `empty() -> EscapeChar` to create `EscapeChar(None)`.
    *   Ensure `EscapeChar` derives `Debug`, `PartialEq`, `Copy`, `Clone`, `Eq`, `Hash`.
*   Support compound expressions:
    *   Ensure `Expr::Like`, `Expr::ILike`, and `Expr::SimilarTo` variants support wrapping in other expressions like `Expr::IsNull`.
*   Accept quantified expressions in pattern fields:
    *   Allow `Expr::Like` and `Expr::ILike` pattern fields to accept quantified expressions.
*   Preserve PostgreSQL operator syntax:
    *   Represent the PostgreSQL operator `~~` as `BinaryOperator::PGLikeMatch`.
    *   Represent the PostgreSQL operator `~~*` as `BinaryOperator::PGILikeMatch`.
    *   Represent the PostgreSQL operator `!~~` as `BinaryOperator::PGNotLikeMatch`.
    *   Represent the PostgreSQL operator `!~~*` as `BinaryOperator::PGNotILikeMatch`.
    *   Ensure these operators are formatted back to their original symbolic forms.
*   Update SQL generator:
    *   Modify `make_bin_op` to map the Like expression type to `BinaryOperator::PGLikeMatch`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.