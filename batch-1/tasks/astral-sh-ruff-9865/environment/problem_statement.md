## Description

The lint rule that detects strings which appear to be missing an interpolation prefix has two categories of bugs: false negatives (cases it should warn about but doesn't) and false positives (cases it warns about incorrectly).

### False Negative

When a string containing brace-formatted variable references is passed to a method that is invoked through attribute access on an object, the rule currently skips it without issuing a warning. This is incorrect — such strings are just as likely to be accidentally non-interpolated as strings passed to plain function calls, so the rule should flag them.

### False Positives

The rule incorrectly warns in three patterns where the string is already handled by explicit formatting:

1. **Grouped concatenation with explicit formatting**: When multiple string parts are implicitly concatenated and the explicit formatting method is applied to the entire grouped result, the rule incorrectly flags the individual parts.

2. **Chained attribute method calls on strings**: When a string literal (or a function result that received the string) has a chain of multiple attribute accesses and method calls applied to it, the rule only suppresses for the first attribute call but not for further chaining.

3. **Positional argument names matching format variables**: When a string is passed to a function alongside positional arguments whose names match the variable references inside the string, the rule does not recognize this as an explicit formatting context and incorrectly warns.

## Expected Behavior

- Strings passed to methods accessed through object attributes should trigger the rule.
- Strings whose concatenated result has explicit formatting applied should not trigger the rule.
- Strings with chained attribute method calls (at any depth) should not trigger the rule.
- Strings passed alongside positional arguments whose names match the brace-delimited variable references should not trigger the rule.

## Why This Matters

These bugs make the rule both miss real issues and generate noise, reducing its usefulness and trustworthiness for codebases that rely on it.
