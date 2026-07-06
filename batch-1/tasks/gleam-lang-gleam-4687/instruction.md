Implement a feature in the Gleam compiler to emit warnings for redundant comparisons anywhere in the code, not just within assertions. Ensure that the warnings clearly indicate whether the comparison always evaluates to true or false, and update existing warning messages for consistency and clarity.

*   Emit a 'Redundant comparison' warning for any comparison expression (==, !=, <, >, <=, >=) that can be statically determined to always produce the same boolean result.
    *   For comparisons always evaluating to true, include the inline hint 'This is always `True`' and the body 'This comparison is redundant since it always succeeds.'
    *   For comparisons always evaluating to false, include the inline hint 'This is always `False`' and the body 'This comparison is redundant since it always fails.'
*   Specific cases for redundant comparisons:
    *   Integer literals: Emit a warning based on the operator and values.
    *   Boolean literals (True or False): Emit a warning for == or !=.
    *   Different string literals: Emit a warning for == or !=.
    *   Variable compared to itself (e.g., `a == a`): Emit a warning; == always succeeds, != always fails.
    *   Record fields accessed from the same variable (e.g., `x.field == x.field`): Emit a warning; == always succeeds, != always fails.
    *   Different constructors of the same custom type (e.g., `Left == Right`): Emit a warning that always fails.
    *   List literals of different lengths: Emit a warning for == or !=.
    *   Structurally identical list literals without function calls: Emit a warning.
    *   List literals with provably different elements: Emit a warning that always fails.
*   Do not emit warnings for:
    *   Comparisons involving function calls, as they may have side effects.
    *   Comparisons between two different variables.
    *   Comparisons of record fields from different variables.
    *   List literals containing function calls in identical positions.
*   Update warnings within assertions:
    *   Assert a comparison of literals: Emit 'Redundant comparison' with the same inline hint and body text.
    *   Assert a literal boolean value (e.g., `assert True`): Emit 'Assertion of a literal value', no inline hint, and body 'Asserting on a literal bool is redundant since you can already tell whether it will be `True` or `False`.'
*   Update the warning for an unused literal value:
    *   Use the title 'Unused literal' and body 'Hint: You can safely remove it.'

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.