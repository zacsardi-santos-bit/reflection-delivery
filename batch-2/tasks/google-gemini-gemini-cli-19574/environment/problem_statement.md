## Description

When AI tools write or modify files, the response currently returns either the full file content or minimal feedback. For large files — say, a file with 100 lines where only one line changed — returning the entire content wastes context space and makes it hard for the model to focus on what actually changed. We need smarter, diff-aware responses.

Similarly, the search tool currently returns match lines without surrounding lines when there are very few results. A single match deep in a large file is hard to interpret without seeing the lines around it.

## Expected Behavior

- When a file write or update is performed, the tool should return a labeled snippet (prefixed with a clear header) that shows only the modified portion(s) of the file plus a few lines of surrounding context. Lines far from any change should be omitted, replaced by a placeholder showing that content was skipped.
- When a file write or update results in few or no changes (e.g., the file is new or tiny), the full content should be returned.
- When the search tool finds 3 or fewer matches in a file, it should include surrounding lines (context before and after each match) to show where in the file the match appears.
- A standalone utility should be available to compute context-aware diff snippets given original and modified text, with a configurable number of surrounding context lines.
- The search tool parameters should support an explicit numeric context option so callers can control how many surrounding lines appear around each match.

## Why This Matters

Returning only changed lines with context makes it much easier to understand the effect of a write operation on a large file. Including context lines around rare search matches improves comprehension of where those matches appear in the file structure. Both improvements reduce unnecessary content in AI responses while preserving the information that matters most.
