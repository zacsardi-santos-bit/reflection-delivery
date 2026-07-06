## Description

When a broker topic is garbage collected because it has no active consumers and no subscriptions, the schema that was registered with that topic remains in the schema registry indefinitely. This results in orphaned schema records that are never cleaned up, even though the topic itself no longer exists.

## Expected Behavior

- When the broker garbage collects a topic (for both persistent and non-persistent topics), the topic's associated schema should also be deleted from the schema registry.
- Topics with active consumers should not be garbage collected, and their schemas should remain intact.
- Topics with existing subscriptions (even when no consumers are currently connected) should not be garbage collected, and their schemas should remain intact.
- Once all subscriptions on a topic are deleted and there are no active consumers, subsequent garbage collection should remove both the topic and its associated schema.

## Why This Matters

Without this cleanup, decommissioned topics leave behind orphaned schema records. Over time, this can pollute the schema registry with stale entries for topics that no longer exist. The fix should ensure schema lifecycle is properly tied to topic lifecycle for all topic types.
