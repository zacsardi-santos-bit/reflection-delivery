Implement support for common Python constructs and improve error handling in the experimental AST refactorer for Taichi kernels. Ensure that augmented assignments, ternary expressions, and static tuple unpacking work correctly. Provide descriptive error messages for static assignment errors and allow flexibility in module import aliases.

*   Augmented Assignment Operators:
    *   Implement the `build_AugAssign(ctx, node) -> node` static method in `python/taichi/lang/ir_builder.py`.
    *   Ensure this method handles all augmented assignment operators (+=, -=, *=, //=, %=, **=, <<=, >>=, |=, ^=, &=, /=).
    *   Evaluate the target and value, apply the operation using the target's `augassign` method, and set `node.ptr` to the result.

*   Ternary Conditional Expressions:
    *   Implement the `build_IfExp(ctx, node) -> node` static method in `python/taichi/lang/ir_builder.py`.
    *   Support both dynamic and static ternary expressions.
        *   For static conditions, evaluate only the selected branch at compile time.
        *   For dynamic conditions, emit frontend control flow and assign the result to an initialized variable, setting `node.ptr` to this variable.

*   Static Tuple Unpacking:
    *   Allow unpacking multiple template arguments into local static variables using `ti.static()`.

*   Error Handling:
    *   Raise a `TaichiSyntaxError` with the message "Static assign cannot be used on elements in arrays" when a static assignment targets an array element.
    *   Raise a `TaichiSyntaxError` with the message "Recreating variables is not allowed" when attempting to re-declare a variable in the current scope using `ti.static()`.

*   Module Import Flexibility:
    *   Ensure all Taichi kernel constructs work correctly regardless of the module import alias.
    *   Modify `get_decorator` and `ASTTransformerChecks` to resolve Taichi constructs using the kernel's actual global variable scope (`ctx.globals`).

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.