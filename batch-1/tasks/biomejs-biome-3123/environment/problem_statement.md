## Description

JavaScript has two sets of string trimming methods: older non-standard aliases introduced by browser vendors years ago, and the modern standardized equivalents now defined in the ECMAScript specification. The standardized names were chosen for their direction-independence and consistency, and are now the recommended approach. The older aliases are considered deprecated but still supported for backwards compatibility.

There is currently no lint rule in the nursery group to detect when code uses the deprecated aliases and guide developers toward the standardized equivalents.

## Expected Behavior

A new lint rule should:
- Detect method calls using the deprecated aliases and report a clear diagnostic message explaining that the deprecated name is an alias for the standard one
- Offer a safe, automated fix that renames the method to the standardized equivalent
- Handle various syntactic access patterns: dot notation, bracket notation with single-quoted strings, double-quoted strings, and template literals
- Correctly ignore cases where the deprecated name appears as a standalone function (no receiver), as the receiver object in a chain, as an argument to another function, inside a constructor expression, with extra arguments, or in computed bracket notation with a variable key

## Why This Matters

Using deprecated aliases makes code less consistent and may cause issues as browsers and runtimes eventually phase out the aliases. An automated lint rule with a safe fix lets developers modernize their codebases without manual search-and-replace, and the fix should correctly handle edge cases like inline comments between the method access and the argument list.
