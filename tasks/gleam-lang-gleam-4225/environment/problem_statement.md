## Description

The Gleam language server should offer a code action to convert the first step of a pipeline expression into an equivalent regular function call. Right now, there is no automated way to make this transformation in the editor — developers have to rewrite the expression manually, which is tedious and error-prone.

## Expected Behavior

- When the cursor is positioned on or near a pipeline expression, a "Convert to function call" code action should be available.
- Invoking the action should rewrite the first step of the pipeline into an equivalent direct function call, with the piped value inserted in the correct argument position.
- The action should handle all standard Gleam pipeline forms:
  - Implicit first-argument insertion (the most common case)
  - Bare function references (no parentheses)
  - Function references with empty parentheses
  - Module-qualified function references
  - Pipelines into functions that themselves return functions (i.e., applying the result to the piped value)
  - Explicit argument holes, whether the placeholder appears in the first position or a later position
- When the pipeline has multiple chained steps, only the first step should be converted; the remaining steps should stay as a pipeline.

## Why This Matters

This makes it easy to toggle between the pipeline style and the direct-call style without manual editing, giving developers a faster workflow when one style is more appropriate than the other in a given context.
