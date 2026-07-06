## Description

When using this file finder tool, there is currently no straightforward way to search for files by their exact name using a plain-text pattern. The existing literal-string search mode matches any file whose name *contains* the pattern as a substring — so searching for something like "a.foo" also returns "aa.foo" and "a.food", which is often not what users want.

To find files by exact name, users currently need to know how to write anchored regular expressions or use glob syntax. This is unnecessarily complex for a simple "find this exact filename" use case.

## Expected Behavior

A new flag should be added that combines literal (non-regex) pattern matching with whole-filename matching:

- Special characters in the pattern (dots, parentheses, etc.) are treated as plain text, not as regex syntax
- The pattern must match the **entire** filename, not just a substring
- Matching is case-insensitive by default, consistent with the tool's existing behavior
- The flag can be combined with the existing case-sensitive option to require an exact-case whole-name match

## Why This Matters

Users who want to find files named exactly "a.foo" get unexpected results from substring-based searches. This new mode fills the gap between the literal-substring mode and the full regex/glob modes, providing a simple, safe way to look up files by their complete name without any special syntax knowledge.
