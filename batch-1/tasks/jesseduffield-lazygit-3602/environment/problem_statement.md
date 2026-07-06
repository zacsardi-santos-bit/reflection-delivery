## Description

The "find base commit for fixup" feature currently fails when a user's working-copy changes consist entirely of added lines with no deleted lines. This is a common situation — for example, inserting a new line into a block of code that was introduced by an earlier commit in the branch. The feature should be able to handle this case by identifying the surrounding context and determining the appropriate base commit, rather than refusing to proceed.

Additionally, the internal logic that classifies diff hunks needs to be improved. Currently, a hunk that contains both deleted and added lines is treated the same as one with only added lines, which is incorrect. A hunk with deletions (even if it also has additions) should be treated as a deletion hunk, with its line count reflecting only the deleted lines.

## Expected Behavior

- When all staged changes are pure additions (no deleted lines), the feature should still successfully identify the base commit by examining the context surrounding each inserted block.
- When a diff hunk contains both deleted and added lines, it should be classified as a deletion hunk (not an addition-only hunk), with the deletion count used for further processing.
- When there are no staged or unstaged changes at all, the feature should still show an appropriate error.
- When changes from multiple different base commits are mixed together, the feature should report that multiple base commits were found, prompting the user to stage a subset of the changes.

## Why This Matters

Users often make changes to their working copy that are purely additive — adding a comment, inserting a new line, or appending to a block — and those changes relate to a specific earlier commit in the branch. Without this fix, the feature is unable to help in these very common cases, forcing the user to manually search through their commit history. With this fix, the feature becomes more broadly useful and handles a larger set of realistic workflows.
