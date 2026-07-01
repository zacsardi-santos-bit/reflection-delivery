## Description

When using JavaScript's built-in string-to-integer parsing function, developers often forget to include the second argument that specifies the numeric base. This can lead to subtle, hard-to-detect bugs because the function's interpretation of the input depends on the string's format when no base is provided. For example, a string that looks like an octal number may be parsed differently across environments.

Beyond the missing-radix case, developers sometimes pass invalid values as the base argument — such as a plain string, a value outside the valid range, or certain non-standard numeric literal types — without realizing the function will silently return a not-a-number value as a result.

## Expected Behavior

- A new lint rule should detect calls to this function that are missing the radix argument and flag them with a clear warning.
- Where the radix is simply absent, an automatic fix should be available to insert the most commonly intended base (10).
- If the provided radix is invalid — wrong type, out of range, or an unsupported literal form — the rule should report it without providing an automatic fix, since the intended value is ambiguous.
- If the function is called with no arguments at all, a separate, distinct warning should be raised indicating that the call will always produce not-a-number.
- Valid calls that include an in-range numeric radix (including those using numeric separator syntax) should not be flagged.
- The rule should apply both to the standalone form and to the form accessed as a property of the built-in number object.

## Why This Matters

Without static analysis catching these patterns, developers can ship code that silently misbehaves or always returns not-a-number without any runtime error. A dedicated lint rule surfaces these bugs at development time without requiring runtime execution.
