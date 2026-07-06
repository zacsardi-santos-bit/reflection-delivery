## Description

When writing Gleam code, it is common to introduce a named variable to hold a simple expression and then use that variable exactly once in the very next statement. Currently, the language server offers no automated refactoring to remove such single-use bindings — developers have to manually delete the binding line and paste the expression at the usage site. This is tedious and error-prone, especially in deeply nested code.

## Expected Behavior

The language server should offer an "Inline variable" code action whenever the cursor is positioned on a variable that:

- Is assigned once via a simple, direct binding (not a destructuring or pattern match)
- Is used exactly once in the same scope

When triggered from either the definition or the usage of the variable, the action should remove the binding and substitute the assigned expression directly at the usage site. The action should work within any scope — top-level function bodies, nested blocks, and blocks inside case clause branches.

The action should deliberately not appear when:
- The variable is referenced more than once (inlining would duplicate the expression)
- The variable was introduced by a complex destructuring pattern
- The variable was bound by a case clause pattern rather than a plain let statement

## Why This Matters

This makes everyday cleanup refactors faster and reduces the friction of keeping code tidy. Instead of reading through several lines to understand a chain of let bindings and manually consolidating them, a developer can inline single-use bindings with a single editor action.
