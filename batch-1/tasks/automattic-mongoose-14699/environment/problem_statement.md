## Description

When implementing custom transaction retry wrappers with Mongoose, there is no way to preserve and restore a document's "dirty" (modified) state around a transaction attempt. After a document is saved inside a transaction that is subsequently aborted, the document's internal record of which fields were modified gets cleared — just as it would after a successful save. If the caller tries to retry the transaction by saving the same document instance again, there are no modifications to send, and the intended changes are silently lost.

## Expected Behavior

- There should be a way to take a snapshot of a document's current modification state before attempting a transaction save.
- There should be a way to restore that snapshot if the transaction is aborted, so the document is back in its "pending changes" state and can be correctly saved in a retry attempt.
- The snapshot and restore operations must work for all types of document paths — top-level fields, nested object fields, single nested subdocuments, and document array elements.
- There should also be a way to fully and explicitly clear a document's modification state on demand, so that a subsequent save writes nothing.

## Why This Matters

Custom transaction retry logic is a common pattern when working with MongoDB sessions. Without the ability to preserve and restore document modification state, developers cannot safely build retry wrappers using Mongoose document instances — they must instead discard the document instance and reload it from the database after each failed attempt, which is cumbersome and potentially incorrect when the document has in-memory state beyond just the modified fields.
