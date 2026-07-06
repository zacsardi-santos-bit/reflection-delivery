## Description

Ozone's prefix access control management has several correctness issues that become visible in high-availability mode. When a leader processes a prefix ACL operation that results in no change (for example, adding an access control entry that already exists, or setting the same access controls again), the in-memory prefix tree is not updated and the transaction log index remains stale. Followers replaying the log then end up with divergent state compared to the leader, which can cause authorization inconsistencies.

There are also related problems with cleanup and validation:

- When the last access control entry is removed from a prefix, the prefix entry is not always deleted from the metadata table and the in-memory prefix tree. The stale entry persists and may interfere with subsequent operations.
- Removing an access control entry from a prefix that does not exist should consistently return a "prefix not found" error, but the current behavior is not reliable.
- Invalid prefix paths — such as those missing the required trailing path delimiter or containing consecutive delimiters — are not rejected early. Instead they propagate to deeper processing and cause unexpected failures.

## Expected Behavior

- All prefix ACL operations (add, set, remove) must always update the in-memory prefix tree and the stored transaction ID, regardless of whether the ACL content actually changed.
- When removing the last ACL from a prefix, the prefix entry must be fully cleaned up from both the in-memory tree and the metadata table.
- Removing an ACL from a prefix that does not exist should return a clear "prefix not found" response.
- Prefix paths without a trailing path delimiter should be rejected with a "prefix not found" status.
- Prefix paths with an invalid filesystem structure should be rejected with an "invalid path" status.

## Why This Matters

Under Ozone Manager high availability, log replay on followers must produce identical in-memory state to the leader. Stale transaction IDs and incomplete cleanup of empty prefixes cause divergent state that is difficult to detect and recover from, and can silently cause incorrect access control decisions.
