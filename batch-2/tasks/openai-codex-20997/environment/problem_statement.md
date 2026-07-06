## Description

When searching memory files, the current implementation returns only the exact line that matched the query. This makes results hard to interpret without opening the full file, since a single line rarely provides enough context. There is also no way to perform case-insensitive searches — every search is case-sensitive with no override available.

## Expected Behavior

- Search results should support an optional number of surrounding context lines. When requested, each match should include the lines immediately before and after the matching line, so the result is self-contained and readable without visiting the full file.
- Each result should report both the line number of the actual match and the starting line number of the context window, so users can precisely navigate to the right location.
- Searches should support a case-insensitive mode that finds all capitalization variants of the query (e.g., matching "needle", "Needle", and "NEEDLE" when searching for "needle" case-insensitively).
- A cursor that points beyond the end of available results should be rejected with an appropriate error rather than silently returning an empty page.

## Why This Matters

Developers using memory file search often need to understand the surrounding context of a match to make sense of it. Without surrounding context lines, every result requires a follow-up read of the full file. Case-insensitive matching is a standard search feature that is currently missing, making it impossible to find content regardless of how it was originally written.
