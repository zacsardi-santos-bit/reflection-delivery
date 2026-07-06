Implement enhancements to the query optimizer for handling primary key lookups in OR-based conditions. This involves updating several functions to improve the extraction and evaluation of primary key values, enabling efficient data block pruning.

*   Update `getPkValueByExpr` in `pkg/vm/engine/disttae/util.go`:
    *   Add a boolean parameter `mustOne`.
    *   Return four values: `(canEval bool, isNull bool, isVec bool, val any)`.
    *   When `mustOne` is true, expressions yielding multiple values must return `(false, false, false, nil)`.
    *   When `mustOne` is false, such expressions must return `(true, false, true, []byte)` with a binary-marshaled vector.
    *   Ensure correct evaluation of OR chains on the primary key, e.g., 'a=2 OR a=1 OR a=3' should return a binary vector containing 1, 2, 3.

*   Update `getPkExpr` in `pkg/vm/engine/disttae/util.go`:
    *   Handle OR expressions on the primary key column.
    *   If both branches of an OR resolve to primary key expressions, return a `plan.Expr` with `Expr_List` containing both sub-results and type `T_tuple`.
    *   If either branch cannot be resolved, return `nil`.
    *   Ensure recursive handling of nested OR/AND expressions.

*   Update `evalExprListToVec` in `pkg/vm/engine/disttae/util.go`:
    *   Accept a type identifier, a `plan.Expr_List` pointer, and a process.
    *   Return `(canEval bool, vec *vector.Vector, put func())`.
    *   For valid lists, evaluate all scalar literals and inline vectors, combine them into a single sorted vector, and provide a cleanup function.
    *   Handle nested `Expr_List` recursively, flattening all values into the output vector.

*   Update `ForeachBlkInObjStatsList` in `pkg/vm/engine/disttae/util.go`:
    *   Change callback signature to `func(blk *objectio.BlockInfo, blkMeta objectio.BlockObject) bool`.
    *   Pass block-level object metadata to the callback.

*   Update `MakePlan2Int64VecExprWithType` in `pkg/sql/plan/make.go`:
    *   Accept a `*mpool.MPool` as the first parameter before variadic int64 values.
    *   Use the vector's `MarshalBinary` format for `LiteralVec.Data`.

*   Remove `pkRange` struct and `computeRangeByNonIntPk` and `computeRangeByIntPk` functions as their logic is no longer needed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.