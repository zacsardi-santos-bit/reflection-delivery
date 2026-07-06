## Description

The language server should offer code actions to automatically generate pattern matching code for function arguments and local variables. This would save developers from having to manually write out destructuring patterns every time they want to work with the inner fields of a tuple or custom type.

Currently, when a developer writes a function that receives a structured value as a parameter, they must manually type out the full pattern binding or case expression to access its contents. The same applies to local let bindings — there is no quick way to expand a variable into its individual parts.

## Expected Behavior

- When the cursor is on a function argument (in both named functions and anonymous functions), a "Pattern match on argument" action should be available.
- For tuple arguments, the action inserts a pattern binding at the top of the function body using auto-generated variable names.
- For single-constructor custom types, the action inserts a let pattern binding. Labelled fields use the label shorthand notation. A single unlabelled field is called "value"; multiple unlabelled fields are numbered starting from 0.
- For types with multiple constructors, the action inserts a case expression with one arm per constructor, each arm containing a placeholder expression.
- The action correctly qualifies constructor names based on how they are imported — using aliases, unqualified names, or module-qualified names as appropriate.
- The action is not offered for empty tuples or for types marked internal that come from another module.
- When the cursor is on a let binding variable, a "Pattern match on variable" action should be available, inserting the same destructuring code after the binding.
- Both actions must handle empty function bodies gracefully and preserve indentation of existing code.

## Why This Matters

Writing out destructuring patterns is repetitive boilerplate. These code actions would let developers immediately start working with the contents of structured values without interrupting their workflow to manually type patterns.
