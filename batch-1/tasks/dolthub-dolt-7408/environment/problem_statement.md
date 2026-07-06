## Description

When viewing commit history in Dolt, users can see who made each commit and when, but there is no way to quickly see a summary of what data actually changed in that commit. For large or busy databases, this makes it hard to assess the scope of a change without checking each commit individually.

## Expected Behavior

- A new flag should be available on the log command that prints a compact diffstat below each commit showing which tables were affected and how many rows were added, modified, or deleted.
- When a table is created in a commit, the output should indicate the table was added.
- When a table is dropped, the output should indicate it was deleted.
- When rows are inserted, updated, or deleted, the output should show the table name alongside a count and a character indicating the type of change, followed by a summary line with counts for each change type.
- The flag should work in combination with the existing compact single-line commit format.
- Merge commits should not show a diffstat, since their changes come from merging branches and the summary would be ambiguous.

## Why This Matters

This mirrors a common workflow in standard version control tools, where developers scan the log to understand which files (or, in Dolt's case, which tables and row counts) changed in each commit. Without this feature, users must look at each commit's diff individually to understand scope, which is slow and cumbersome.
