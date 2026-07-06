## Description

The markdown formatter incorrectly preserves trailing spaces on the last line of a paragraph. In markdown, two trailing spaces on a line create a "hard line break" that forces a new line within the same paragraph. However, when this trailing-space sequence appears on the **last line** of a paragraph block (i.e., the line is followed by a blank line or the end of the document), those trailing spaces are meaningless — there is no next line to break to. The formatter should strip them in this case, just as the reference formatter does.

## Expected Behavior

- When trailing spaces (hard line break markers) appear on an **interior line** of a paragraph, they should be preserved in the formatted output — they create an actual line break.
- When trailing spaces appear on the **last line** of a paragraph block, they should be removed from the formatted output — they serve no purpose there.
- This behavior should be consistent whether the paragraph ends with a blank line or at end-of-file.

## Current Behavior

The formatter keeps trailing spaces on the last line of a paragraph, causing the output to differ from the reference formatter in these cases.

## Why This Matters

This inconsistency means documents formatted by the tool may contain semantically meaningless whitespace that differs from what other formatters produce. Achieving compatibility with the reference formatter for these edge cases is important for correctness and interoperability.
