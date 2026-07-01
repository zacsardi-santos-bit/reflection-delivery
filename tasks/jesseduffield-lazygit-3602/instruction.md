Implement the "find base commit for fixup" feature in lazygit to handle diffs with only added lines and improve the classification of diff hunks. Ensure the feature can identify the base commit by examining the context of added lines and correctly classify hunks with both deleted and added lines.

*   Update the `parseDiff` function in `pkg/gui/controllers/helpers/fixup_helper.go`:
    *   Accept a git diff string and return two slices of hunk pointers.
    *   Return two non-nil empty slices when the input diff string is empty.
    *   Place hunks with only deleted lines in the first return slice, setting `numLines` to the count of deleted lines.
    *   Place hunks with both deleted and added lines in the first return slice, setting `numLines` to the count of deleted lines only.
    *   Place hunks with only added lines in the second return slice, setting `numLines` to the count of added lines.
    *   Ensure each hunk records the filename and the `startLineIdx` from the '-N' side of the hunk header.
    *   Correctly process diffs spanning multiple hunks across multiple files, categorizing each hunk appropriately.

*   Ensure the feature:
    *   Identifies the base commit when all staged changes are pure additions by examining the surrounding context.
    *   Classifies a hunk with both deleted and added lines as a deletion hunk, using the deletion count for processing.
    *   Shows an appropriate error when there are no staged or unstaged changes.
    *   Reports multiple base commits when changes from different base commits are mixed, prompting the user to stage a subset of changes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.