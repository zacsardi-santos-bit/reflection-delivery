Implement methods to manage the modified state of Mongoose documents, allowing for transaction retries. Ensure you can take a snapshot of a document's modifications, restore it if needed, and clear modifications entirely.

*   Implement the `$clearModifiedPaths()` method in `lib/document.js`.
    *   Ensure it clears all modified paths on the document, including nested subdocuments and document array elements.
    *   Verify that calling `getChanges()` on the root document and any subdocuments immediately after returns an empty object.
    *   Ensure that saving the document after clearing does not write any previously pending changes to the database.

*   Implement the `$createModifiedPathsSnapshot()` method in `lib/document.js`.
    *   Capture and return a snapshot of the document's current modified paths state.
    *   Include the state of all nested subdocuments and document array elements in the snapshot.

*   Implement the `$restoreModifiedPathsSnapshot(snapshot)` method in `lib/document.js`.
    *   Accept a snapshot object created by `$createModifiedPathsSnapshot()`.
    *   Restore the document's modified paths state to match the snapshot.
    *   Ensure `getChanges()` on nested subdocuments reflects the snapshot state.
    *   Confirm that saving the document after restoring persists all changes present at the snapshot time.

*   Ensure that using `$createModifiedPathsSnapshot()` before a transaction save and `$restoreModifiedPathsSnapshot(snapshot)` after aborting allows the document to be saved again in a new transaction, correctly persisting the original changes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.