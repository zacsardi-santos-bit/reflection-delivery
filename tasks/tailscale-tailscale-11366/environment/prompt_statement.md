I'm working on the TailFS share management code and need to make several improvements. Right now, shares are stored and returned in an unordered way, so every time the list is shown it might appear in a different order. I'd like shares to always be maintained in consistent alphabetical order.

I also need to add a rename operation. Currently there's no way to rename a share — you have to delete it and add it back with a new name, which is clunky. A proper rename should atomically rename an existing share, fail if the old name doesn't exist, fail if the new name is already taken, and fail if the new name contains invalid characters.

Another issue is that some important error conditions — like "the feature isn't enabled" and "the share name is invalid" — are returned as private, unexported errors. Since callers outside the package need to be able to detect and handle these specific conditions, these errors need to be made publicly accessible.

The internal representation also needs to change from a map to a sorted slice, which means the interface that the file system implementation uses to receive share updates needs to be updated to accept a sorted slice rather than a map. Any code building that slice should sort it before passing it along.

Finally, shares should be stored as part of the user's regular preferences rather than a separate state store. The preference system needs to know about this field, but it should not expose it as a flag in the general-purpose connection command — it belongs to the dedicated share management subcommand.
