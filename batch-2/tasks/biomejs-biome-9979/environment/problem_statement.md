## Description

The markdown formatter does not normalize fenced code block delimiters to a consistent style. If a document uses tilde-based fences (four or more tilde characters), the formatter leaves them unchanged instead of converting them to the standard backtick style. This causes the formatter to diverge from widely-adopted markdown formatting conventions and from what popular formatters produce.

In addition, when a fenced code block has a language or metadata tag (the info string after the opening fence), the formatter has several issues:

- Trailing whitespace in the info string is not removed.
- A spurious leading space is inserted between the fence characters and the info string.
- Info strings using comma-separated or multi-value formats (common in documentation tooling) are not parsed correctly and may be garbled or lost.

## Expected Behavior

- Tilde-delimited fenced code blocks should be reformatted to use backtick-style delimiters.
- Info strings should have trailing whitespace stripped.
- Info strings should be written immediately after the fence characters, with no leading space.
- Info strings with comma-separated or multi-value formats should be preserved correctly.

## Why This Matters

Inconsistent fence delimiter style increases unnecessary diffs when mixing files written with different conventions. The leading-space and trailing-whitespace issues in info strings cause reformatted output to not match what documentation tools expect, and the broken handling of comma-separated info strings silently drops metadata that some toolchains rely on.
