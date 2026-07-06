Update the lint rule to detect missing explicit return statements in functions where the else branch ends with a context manager block without an explicit return. Implement the necessary changes to ensure the linter flags these cases and suggests a fix.

*   Modify the rule implementation in `crates/ruff_linter/src/rules/flake8_return/rules/function.rs`:
    *   Extend implicit-return detection to recurse into context manager blocks in else branches.
    *   Change the `function` entry point to accept a single `&ast::StmtFunctionDef` reference:
        *   Old signature: `pub(crate) fn function(checker: &mut Checker, body: &[Stmt], decorator_list: &[Decorator], returns: Option<&Expr>)`
        *   New signature: `pub(crate) fn function(checker: &mut Checker, function_def: &ast::StmtFunctionDef)`
    *   Differentiate behavior between stable and preview modes:
        *   Stable mode: Emit diagnostic at the implicit-return statement, insert `return None` at the same indentation.
        *   Preview mode: Emit diagnostic for the entire function, insert `return None` at the function body indentation level.

*   Update the call site in `crates/ruff_linter/src/checkers/ast/analyze/statement.rs`:
    *   Pass the full `function_def` node to `flake8_return::rules::function`.

*   Update the regular snapshot file `crates/ruff_linter/src/rules/flake8_return/snapshots/ruff_linter__rules__flake8_return__tests__RET503_RET503.py.snap`:
    *   Include the new diagnostic for the added fixture function (`def f()`).
    *   Ensure the diagnostic is at the last statement inside the `with` block, with the fix inserting `return None` at the same indentation level.

*   Register a new preview-mode test case in `crates/ruff_linter/src/rules/flake8_return/mod.rs`:
    *   Add `#[test_case(Rule::ImplicitReturn, Path::new("RET503.py"))]` to the `preview_rules` test section.

*   Create a new preview snapshot file at `crates/ruff_linter/src/rules/flake8_return/snapshots/ruff_linter__rules__flake8_return__tests__preview__RET503_RET503.py.snap`:
    *   Cover all functions in the RET503.py fixture that trigger the rule.
    *   In preview mode, ensure diagnostics span the entire function definition, with fixes inserting `return None` at the function body indentation level.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.