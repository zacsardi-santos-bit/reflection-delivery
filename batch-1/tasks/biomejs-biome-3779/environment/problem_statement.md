## Description

We need a new CSS lint rule to catch a common mistake: referencing a declared CSS custom property as a plain value instead of wrapping it with the proper accessor function. When a developer defines a custom property and then tries to use it directly (e.g., writing the property name directly as a value), the browser silently ignores it rather than applying the variable's value. This is a frequent source of invisible bugs in CSS.

The rule should only flag cases where the custom property is actually declared somewhere in accessible scope — either in the same block, in an ancestor selector, or globally via a property registration. If the custom property hasn't been declared anywhere, the reference might just be an intentional custom identifier used in a specific context, and the rule should leave it alone.

## Expected Behavior

- Flag CSS custom properties that are declared within the accessible scope (current block, parent selectors, or global scope) but referenced as raw values without the accessor wrapper function.
- Do not flag references to undeclared custom properties.
- Do not flag custom properties that are already correctly wrapped.
- Do not flag custom properties appearing in certain CSS properties where bare custom names are semantically valid (such as transition-related, animation, grid area, counter, and view transition properties).
- Handle nested CSS selectors correctly: a property declared in a parent is accessible to children (and should be flagged if not wrapped), but a property declared only in a child is not accessible to the parent.

## Why This Matters

This kind of mistake produces no browser error — the property value is simply ignored — making it very difficult to spot during development. A linter that can catch it automatically saves developers from silent CSS failures.

Additionally, the existing rule that detects dependencies used in JavaScript/TypeScript files but absent from the package manifest should be promoted out of the experimental/nursery category into the stable correctness category, so teams can include it in production configurations without encountering a configuration error.
