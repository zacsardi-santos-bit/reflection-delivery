## Description

The Sway code formatter is incorrectly modifying the indentation of line comments that appear between statements inside function bodies. When a developer writes well-formatted code with comments interspersed between statements in an implementation block, running the formatter causes those comments to end up at the wrong indentation level. The issue is especially pronounced when comments contain multi-byte Unicode characters (such as mathematical comparison symbols).

## Expected Behavior

- When the formatter runs on already-correctly-formatted code, it should produce output that is identical to the input (idempotent behavior).
- Line comments between statements in a function body should stay at the same indentation level as the surrounding statements.
- Comments appearing after block expressions (such as conditional branches) inside function bodies should also maintain the correct indentation.
- Comments containing non-ASCII characters must be handled just as reliably as those containing only ASCII characters.

## Current Behavior

Running the formatter on code that contains line comments with multi-byte characters between statements in an impl function body results in those lines being shifted to incorrect indentation. This corrupts the formatting of subsequent lines as well.

## Why This Matters

This bug effectively means the formatter actively makes code worse — a developer who runs the formatter trusting it to preserve well-structured code will instead end up with broken indentation. It undermines the utility of the auto-formatter and makes it unsafe to run on code containing Unicode characters in comments.
