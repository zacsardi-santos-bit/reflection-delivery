## Description

When using "contains" assertion types to check whether an AI model's output includes a specific phrase that itself contains a comma — such as "hello, world" — the assertion logic incorrectly splits the value at every comma. This causes the phrase to be treated as multiple separate tokens instead of a single value to match against. The result is both false positives (matching on partial tokens that were never intended as independent values) and incorrect failures (not matching the intended full phrase).

## Expected Behavior

- Values provided as comma-separated lists for "contains" assertions should support standard CSV-style quoting: wrapping a value in double quotes should prevent the commas inside from being treated as separators.
- Both the "any" variant (pass if at least one value matches) and the "all" variant (pass only if every value matches) should parse the value list with this quoting behavior.
- Both case-sensitive and case-insensitive variants should behave the same way.
- Special characters inside quoted values — such as literal quote characters and backslash characters — should be correctly unescaped.
- Whitespace characters like tabs between fields should not produce spurious empty tokens.

## Why This Matters

Users who need to assert that AI output contains comma-containing phrases (proper nouns, formatted data, etc.) currently have no way to do so reliably. The parser silently produces wrong results, leading to incorrect test outcomes that are difficult to debug.
