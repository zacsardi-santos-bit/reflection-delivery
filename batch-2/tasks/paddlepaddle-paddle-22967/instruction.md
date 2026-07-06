Refactor the dynamic-to-static graph conversion tool to correctly handle conditional expressions that combine tensor-based checks with regular Python comparisons using logical operators. Implement a visitor-based interface to determine if a conditional expression requires static graph control flow and transform compound conditions into static graph logical operations.

*   Implement the `IfConditionVisitor` class in `python/paddle/fluid/dygraph/dygraph_to_static/ifelse_transformer.py`.
    *   Accept a `gast.AST` node as the first argument and an optional `static_analysis_visitor` as the second argument.
    *   Raise an Exception with the message 'Type of input node should be gast.AST' if the first argument is not a `gast.AST` node.
    *   Provide an `is_control_flow()` method:
        *   Return `False` for plain Python expressions (e.g., arithmetic, None checks).
        *   Return `True` for conditions involving tensor operations requiring static control flow.
    *   Provide a `transform()` method:
        *   Return a tuple `(new_node, assign_nodes)`.
        *   Return the original node and an empty list if `is_control_flow()` is `False`.
        *   Return the original node and an empty list for simple tensor comparisons without logical operators.
        *   Return a `gast.Name` node and a non-empty list of assign nodes for compound conditions with logical operators mixing tensor comparisons and Python values.
        *   Ensure `transform()` returns exactly 2 `assign_nodes` for two-operand conditions and 4 `assign_nodes` for three-operand chained conditions.

*   Implement the `IsControlFlowVisitor` class in `python/paddle/fluid/dygraph/dygraph_to_static/ifelse_transformer.py`.
    *   Accept a `gast.AST` node as the first argument and an optional `node_var_type_map` keyword argument.
    *   Provide a `transform()` method:
        *   Return `True` if any variable in the expression is mapped to `NodeVarType.TENSOR`.
        *   Return `False` if variables are mapped to `NodeVarType.NUMPY_NDARRAY`.

*   Ensure `NodeVarType` is importable from `paddle.fluid.dygraph.dygraph_to_static.static_analysis` with at least `TENSOR` and `NUMPY_NDARRAY` attributes.

*   Maintain the `get_name_ids` function importable from `paddle.fluid.dygraph.dygraph_to_static.ifelse_transformer`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.