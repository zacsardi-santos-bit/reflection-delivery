I'm working on refactoring the expression evaluation code in Dolt's SQL layer.

*   The EqualsOp, GreaterOp, GreaterEqualOp, LessOp, and LessEqualOp comparison operator types must be empty structs with no fields (no value read-writer or other stored state). They must be instantiable as zero-value struct literals with no constructor arguments.

*   The CompareOp interface must only contain two methods: CompareToNil(otherIsNull bool) bool and ApplyCmp(n int) bool. The older methods CompareLiterals, CompareNomsValues, and the old CompareToNil(types.Value) (bool, error) signature must be replaced.

*   Each comparison operator must implement CompareToNil(otherIsNull bool) bool (returns a plain bool, not bool+error). EqualsOp.CompareToNil(true) must return true (null equals null); EqualsOp.CompareToNil(false) must return false. All other operators (GreaterOp, GreaterEqualOp, LessOp, LessEqualOp) must return false for any value of the argument.

*   Each comparison operator must implement ApplyCmp(n int) bool. EqualsOp returns true only when n == 0. GreaterOp returns true only when n > 0. GreaterEqualOp returns true when n >= 0. LessOp returns true only when n < 0. LessEqualOp returns true when n <= 0.

*   The ExpressionFunc type must be defined as func(ctx *sql.Context, row sql.Row) (bool, error). The old definition using context.Context and map[uint64]types.Value must be replaced.

*   newComparisonFunc must have the signature newComparisonFunc(ctx *sql.Context, op CompareOp, exp expression.BinaryExpression, sch sql.Schema) (ExpressionFunc, error). It must accept sql.Schema (not the internal dolt schema.Schema type) and must return ExpressionFunc.

*   newOrFunc and newAndFunc must accept and return ExpressionFunc values (i.e., func(*sql.Context, sql.Row) (bool, error)), using the updated row-based signature.

*   ExpressionFuncFromSQLExpressions must have signature ExpressionFuncFromSQLExpressions(ctx *sql.Context, sch sql.Schema, expressions []sql.Expression) (ExpressionFunc, error). The vr types.ValueReader parameter previously present must be removed.

*   When null is involved in a comparison (column or compared value is null), the result for all operators must be false, except for EqualsOp when both sides are null (i.e., CompareToNil(true) is true for EqualsOp only). This null-handling must be reflected in the row-based comparison functions.

*   The literal_helpers.go file and its associated tests must be deleted. The internal helper functions for converting literals to noms values (e.g., LiteralToNomsValue and related helpers) are removed as part of this change.


*   Interface details: Type: Interface
Name: CompareOp
Location: go/libraries/doltcore/sqle/expreval/compare_ops.go
Description: Interface implemented by all comparison operators. The interface must only contain ApplyCmp and CompareToNil methods (the older CompareLiterals, CompareNomsValues methods must be removed).
Signature:
  ApplyCmp(n int) bool
  CompareToNil(otherIsNull bool) bool

Type: Struct
Name: EqualsOp
Location: go/libraries/doltcore/sqle/expreval/compare_ops.go
Description: Implements CompareOp for equality comparison. Must be an empty struct (no fields).
Signature:
  ApplyCmp(n int) bool                 — returns true only when n == 0
  CompareToNil(otherIsNull bool) bool  — returns true only when otherIsNull is true

Type: Struct
Name: GreaterOp
Location: go/libraries/doltcore/sqle/expreval/compare_ops.go
Description: Implements CompareOp for greater-than comparison. Must be an empty struct (no fields, no vr/vrw field).
Signature:
  ApplyCmp(n int) bool                 — returns true only when n > 0
  CompareToNil(otherIsNull bool) bool  — always returns false

Type: Struct
Name: GreaterEqualOp
Location: go/libraries/doltcore/sqle/expreval/compare_ops.go
Description: Implements CompareOp for greater-than-or-equal comparison. Must be an empty struct (no fields, no vr/vrw field).
Signature:
  ApplyCmp(n int) bool                 — returns true when n >= 0
  CompareToNil(otherIsNull bool) bool  — always returns false

Type: Struct
Name: LessOp
Location: go/libraries/doltcore/sqle/expreval/compare_ops.go
Description: Implements CompareOp for less-than comparison. Must be an empty struct (no fields, no vr/vrw field).
Signature:
  ApplyCmp(n int) bool                 — returns true only when n < 0
  CompareToNil(otherIsNull bool) bool  — always returns false

Type: Struct
Name: LessEqualOp
Location: go/libraries/doltcore/sqle/expreval/compare_ops.go
Description: Implements CompareOp for less-than-or-equal comparison. Must be an empty struct (no fields, no vr/vrw field).
Signature:
  ApplyCmp(n int) bool                 — returns true when n <= 0
  CompareToNil(otherIsNull bool) bool  — always returns false

Type: TypeAlias
Name: ExpressionFunc
Location: go/libraries/doltcore/sqle/expreval/expression_evaluator.go
Description: A function type that takes a sql.Context and sql.Row and returns whether some filtering criteria are satisfied.
Signature: type ExpressionFunc func(ctx *sql.Context, row sql.Row) (bool, error)

Type: Function
Name: newComparisonFunc
Location: go/libraries/doltcore/sqle/expreval/expression_evaluator.go
Signature: newComparisonFunc(ctx *sql.Context, op CompareOp, exp expression.BinaryExpression, sch sql.Schema) (ExpressionFunc, error)
Description: Creates a comparison predicate (ExpressionFunc) from a SQL context, comparison operator, binary expression, and sql.Schema. Must accept sql.Schema (from go-mysql-server) rather than the internal dolt schema type.

Type: Function
Name: newOrFunc
Location: go/libraries/doltcore/sqle/expreval/expression_evaluator.go
Signature: newOrFunc(left ExpressionFunc, right ExpressionFunc) ExpressionFunc
Description: Combines two ExpressionFunc values with logical OR.

Type: Function
Name: newAndFunc
Location: go/libraries/doltcore/sqle/expreval/expression_evaluator.go
Signature: newAndFunc(left ExpressionFunc, right ExpressionFunc) ExpressionFunc
Description: Combines two ExpressionFunc values with logical AND.

Type: Function
Name: ExpressionFuncFromSQLExpressions
Location: go/libraries/doltcore/sqle/expreval/expression_evaluator.go
Signature: ExpressionFuncFromSQLExpressions(ctx *sql.Context, sch sql.Schema, expressions []sql.Expression) (ExpressionFunc, error)
Description: Public API that builds an ExpressionFunc from a slice of sql.Expression values. The vr (value reader) parameter that previously appeared in the old signature must be removed; only ctx, sch, and expressions are accepted.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.