## Description

The docstring linter fails to recognize section headers that are written in all-lowercase when they appear after a blank line in a docstring. For example, when a docstring contains a properly capitalized section followed by a blank line and then the same section keyword in all-lowercase, the linter should identify the lowercase entry as a section header (triggering capitalization rules and other section-related formatting checks). Instead, it silently treats it as a subsection and skips all relevant checks.

## Expected Behavior

- A known section keyword followed by a colon that appears after a blank line should be recognized as a section header, regardless of whether it is title-cased or all-lowercase.
- When recognized as a section header, all applicable lint rules should fire — including reporting that the section name is not properly capitalized and flagging any missing blank lines after the last section.
- The existing behavior for lowercase section keywords that appear directly after other section content (true subsections) should remain unchanged.

## Why This Matters

Users who write lowercase section headers in their docstrings currently receive no feedback from the linter about the capitalization problem, because the section is not even recognized. This leads to inconsistently formatted docstrings that silently pass linting checks. The fix ensures users get accurate and complete lint feedback for their docstrings.
