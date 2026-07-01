# Multiple Simultaneous Document Changes Cause Incorrect Diagnostics

## Description

When using the Postgres language server with an editor that sends multiple text changes in a single notification (such as a rename refactor that updates the same identifier in several places at once), the language server incorrectly applies those changes. The second and subsequent changes in the batch end up at the wrong position in the document, because the server converts all change positions using the original document layout rather than accounting for how earlier changes in the same batch shifted the text.

## Expected Behavior

- When a single notification contains multiple range-based edits, all edits should be applied at their correct positions relative to the document state as each edit is processed.
- After a batch of edits that produces a valid SQL statement referencing tables that exist in the database, no error diagnostics should be reported.

## Current Behavior

- After applying multiple simultaneous changes, the document content ends up in an incorrect state.
- The language server then reports false positive diagnostics (e.g., referencing an unknown table) because the second change was applied at the wrong offset.

## Why This Matters

Editors commonly batch multiple related edits into a single change notification — for example, when renaming a symbol that appears on multiple lines. If the language server mishandles these batched changes, it produces spurious errors that confuse developers and undermine trust in the tool's diagnostics.
