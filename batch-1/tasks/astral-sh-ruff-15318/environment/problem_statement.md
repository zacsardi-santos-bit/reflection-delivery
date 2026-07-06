## Description

Two related lint rules are incorrectly handling regex patterns that contain unescaped closing parentheses.

**False positive in the "unnecessary regular expression" rule:** When a regex module function is called with a pattern that contains a closing parenthesis, the linter incorrectly suggests replacing the call with a plain string operation. This suggestion is wrong because the closing parenthesis is a meaningful regex metacharacter — following the suggestion would silently change program behavior.

**False negative in the "ambiguous test assertion pattern" rule:** When a test exception context manager uses a plain (non-raw, unescaped) string match pattern that contains a closing parenthesis, the linter fails to warn about the ambiguity. A closing parenthesis has special meaning in regular expressions, so using it unescaped in a plain string match pattern is potentially confusing and deserves a warning.

## Root Cause

Both rules share an internal list of characters recognized as regex metacharacters. The closing parenthesis is missing from this list, even though it is a valid and meaningful regex metacharacter.

## Expected Behavior

- Patterns containing an unescaped closing parenthesis should be treated as having regex significance
- The "unnecessary regular expression" rule should not flag patterns containing a closing parenthesis for replacement with plain string operations
- The "ambiguous test assertion pattern" rule should warn when a non-raw, unescaped match string contains a closing parenthesis

## Why This Matters

Without this fix, developers may receive incorrect suggestions to remove regex calls that are actually needed, or miss warnings about ambiguous match patterns — both of which can lead to subtle bugs.
