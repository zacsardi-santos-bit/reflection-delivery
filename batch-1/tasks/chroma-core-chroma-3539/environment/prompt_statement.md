I'm working on the garbage collection system for collection version histories and need to implement a two-phase cleanup protocol. Right now there's no way to safely mark old versions for deletion before actually cleaning them up, which means GC can't record its intent durably before it starts removing data.

I need two new operations on the catalog layer: one that marks specific versions within a collection's version history as pending deletion (without removing them yet), and another that actually removes those version entries permanently from the history once cleanup is done. Both operations should handle multiple collections at once, and each collection's outcome should be tracked independently — if a collection isn't found or the requested versions don't exist in the history, that should be reported as a failure for just that collection rather than causing the whole call to fail.

I also need a helper to retrieve the current version file name for a collection, since downstream callers need this after performing the mark or delete operations.

On the infrastructure side, the object storage interface needs a method to delete a single version file by name, and the database layer needs a compare-and-swap operation for atomically updating the version file name — succeeding only if the current value matches what the caller expects.

Finally, the underlying protocol definition needs new message types for these operations, along with new fields on the version history entries to track the pending deletion flag and the version file name.
