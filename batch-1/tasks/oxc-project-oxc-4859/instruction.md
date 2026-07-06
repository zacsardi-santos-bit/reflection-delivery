Update the internal representation of a function call expression to ensure the callee is visited before its arguments during semantic analysis. Adjust the field order in the `CallExpression` struct and update all related builder functions and call sites to reflect this change.

*   Modify the `CallExpression` struct in `crates/oxc_ast/src/ast/js.rs`:
    *   Reorder fields to: span, callee, type_parameters, arguments, optional.
*   Update the visitor walker in `crates/oxc_ast/src/generated/visit.rs`:
    *   Ensure `walk_call_expression` visits the callee before the arguments.
*   Adjust builder functions in `crates/oxc_ast/src/generated/ast_builder.rs`:
    *   Update `call_expression`, `expression_call`, `alloc_call_expression`, and `chain_element_call_expression` to use the new parameter order: span, callee, type_parameters, arguments, optional.
*   Revise layout assertions in `crates/oxc_ast/src/generated/assert_layouts.rs`:
    *   Reflect new field ordering offsets for callee, type_parameters, and arguments.
*   Update all call sites constructing a `CallExpression`:
    *   Ensure they use the new field/parameter order.
*   Verify semantic analysis snapshots:
    *   Ensure `call-expression.snap` shows callee (`foo`) references with id=0, node_id=13 and id=2, node_id=19, and argument (`a`) references with id=1, node_id=15 and id=3, node_id=21.
    *   Ensure `method-param-default.snap` shows `printerName` reference with node_id=40.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.