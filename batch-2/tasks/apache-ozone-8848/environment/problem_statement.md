## Description

When clients write objects to an Ozone cluster, they typically pre-allocate more storage blocks than they end up using. During the commit phase, only the blocks the client actually wrote are finalized — the remaining pre-allocated blocks become orphaned and should be scheduled for garbage collection. Currently, this cleanup is not happening correctly in all scenarios, leading to storage leaks.

There are two specific gaps:

1. **Multipart upload part commits**: When committing a part of a multipart upload, any pre-allocated blocks that were not actually committed are not being queued for deletion. These blocks become permanently orphaned.

2. **Key/part overwrites with uncommitted blocks**: When an existing key (or multipart upload part) is being overwritten at the same time that the new write also has uncommitted blocks, neither the old version's blocks nor the uncommitted blocks from the new write are fully accounted for in the cleanup queue. For regular key overwrites with uncommitted blocks, both should be tracked together in one delete entry. For multipart upload part overwrites with uncommitted blocks, each set of orphaned blocks should be tracked separately.

## Expected Behavior

- When a commit has fewer confirmed blocks than were allocated, the surplus allocated blocks must be scheduled for deletion.
- For multipart upload part commits with only uncommitted blocks (no overwrite), the response must carry exactly 1 deletion entry for the uncommitted blocks.
- For multipart upload part overwrites without uncommitted blocks, the response must carry exactly 1 deletion entry for the overwritten part.
- For multipart upload part overwrites that also have uncommitted blocks, the response must carry 2 separate deletion entries.
- For regular key overwrites with uncommitted blocks, the response must carry 1 deletion entry containing both the overwritten key and the uncommitted pseudo-key.
- All deletion entries must be persisted to the deleted table during the database batch write.

## Why This Matters

Storage blocks that are never reclaimed cause cluster-wide storage leaks. Over time, repeated uploads with unused pre-allocated blocks or overwrite operations silently consume storage capacity that can never be recovered without manual intervention.
