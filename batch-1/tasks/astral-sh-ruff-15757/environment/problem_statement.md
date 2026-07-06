## Description

The lint rule that detects for-loops repeatedly calling a file object's write method — and suggests replacing them with a single bulk-write call — incorrectly fires in several situations where the simplification would silently change the program's behavior.

## Cases Where the Rule Should Not Fire (But Currently Does)

- **Global scope variable**: When the loop target variable is declared as a global variable in the enclosing scope, the loop assignment reaches the global scope on every iteration. Replacing the loop with a bulk-write operation loses that side effect.
- **Nonlocal variable**: Same issue when the loop variable is declared nonlocal inside a nested function — the assignment propagates upward and must not be eliminated.
- **Prior assignment**: When a variable with the same name as the loop target was already assigned a value earlier in the same scope, removing the loop would discard the semantics of overwriting that prior binding.
- **Variable used after the loop**: When any variable introduced by the loop target (including variables nested inside destructuring patterns like tuple or list unpacking) is referenced in code that follows the loop, the loop's final iteration value must be preserved. Replacing the loop would eliminate the variable binding.
- **Global variable in nested pattern**: When the loop target is a destructuring pattern (such as a nested list or tuple) and one of the nested names is declared global, the rule should still skip it for the same reason as the simple global case.

## Cases Where the Rule Should Still Fire (New Tests Added)

The rule correctly handles complex loop targets — including empty tuple patterns, multi-variable tuple unpacking, and arbitrarily nested list/tuple destructuring — and should flag these as valid candidates. Explicit test coverage has been added for these patterns to confirm the behavior.

## Expected Behavior

The rule should detect for-loop writes and suggest bulk-write replacements only when the transformation is safe: none of the loop target variables have broader scope (global or nonlocal), none existed before the loop, and none are referenced after the loop ends.
