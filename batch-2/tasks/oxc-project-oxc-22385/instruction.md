I'm getting incorrect diagnostic messages from the unused variable lint rule when working with rest parameters in TypeScript.

*   When a rest parameter's name is referenced only in a type query within its own type annotation (e.g., the rest param name appearing after 'typeof' in the parameter's type), the no-unused-vars lint rule must produce a diagnostic stating the parameter is 'declared but only used as a type' rather than 'declared but never used'. The full message must be: "Parameter '<name>' is declared but only used as a type. Unused parameters should start with a '_'."

*   When a rest parameter's name is referenced only in a return type predicate (e.g., used in a type guard predicate like 'args is string[]' in the return type), the no-unused-vars lint rule must produce a diagnostic stating the parameter is 'declared but only used as a type' rather than 'declared but never used'. The full message must be: "Parameter '<name>' is declared but only used as a type. Unused parameters should start with a '_'."

*   When a rest parameter does not match the configured 'argsIgnorePattern' option, the no-unused-vars lint rule must produce a diagnostic that references the argument-specific ignore pattern in its suggestion. The full message must be: "Parameter '<name>' is declared but never used. Unused parameters should match /<argsIgnorePattern>/." The rule must NOT use the vars ignore pattern for rest parameter diagnostics.

*   The fix must be applied in the no-unused-vars rule at 'crates/oxc_linter/src/rules/eslint/no_unused_vars/'. The function that checks whether a symbol is used in a return type predicate must handle rest parameters in addition to regular formal parameters.


*   Interface details: Type: Function
Name: is_used_in_return_type_predicate
Location: crates/oxc_linter/src/rules/eslint/no_unused_vars/usage.rs
Signature: is_used_in_return_type_predicate(&self) -> bool
Description: Returns true if this symbol (parameter) is referenced only in a return type predicate. Must handle both regular formal parameters (FormalParameter AST kind) and rest parameters (FormalParameterRest AST kind). Previously only handled FormalParameter, causing rest parameters to always return false and be misclassified as "never used" instead of "only used as a type".

Type: Function
Name: diagnostic::param (called within NoUnusedVars rule for BindingRestElement)
Location: crates/oxc_linter/src/rules/eslint/no_unused_vars/mod.rs
Signature: diagnostic::param(symbol, ignore_pattern, is_type_only: bool)
Description: When called for a rest parameter (BindingRestElement AST kind), the ignore_pattern argument must be the args_ignore_pattern (not vars_ignore_pattern), and the is_type_only boolean must be computed as (symbol.is_used_in_return_type_predicate() || symbol.has_reference_used_as_type_query()). This ensures rest parameters get correct diagnostic messages matching the behavior of regular formal parameters.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.