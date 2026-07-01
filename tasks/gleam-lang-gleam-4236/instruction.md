Implement a new refactoring action in the Gleam language server to inline variables. This action should be available when a local variable is defined by a simple let binding and used exactly once immediately after. Ensure the action removes the binding and substitutes the expression at the usage site.

*   Register a code action titled "Inline variable" in the language server.
*   Offer the "Inline variable" action when:
    *   The cursor is on or near a variable name.
    *   The variable is defined via a simple let binding.
    *   The variable is used exactly once in its scope.
*   Do not offer the action when:
    *   The variable is defined via a destructuring pattern.
    *   The variable is bound by a case clause pattern match.
    *   The variable appears more than once in its scope.
*   Ensure the action can be triggered from:
    *   The definition site (cursor on the variable name in the let binding).
    *   The usage site (cursor on the variable reference where it is used).
*   When the action is applied:
    *   Remove the entire let binding statement.
    *   Replace the single usage of the variable with the expression assigned to it.
*   Ensure functionality within:
    *   Nested block scopes.
    *   Blocks within case clause branches.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.