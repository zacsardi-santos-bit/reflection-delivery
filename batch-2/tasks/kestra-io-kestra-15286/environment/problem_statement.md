## Description

When a flow is updated but only its tasks change (while all its triggers remain identical between revisions), the system currently does not emit any event for those unchanged triggers. As a result, other components that rely on trigger events to refresh their cached state are never notified, leaving them with stale trigger data even though the flow has been updated.

## Expected Behavior

- When a flow update does not modify any of its triggers, the system should still emit a notification event for each of those unchanged triggers, signaling that a refresh is needed.
- A utility should be available to compare two revisions of a flow and determine which triggers are completely unchanged — i.e., present in both revisions with the same identity and identical configuration. Triggers that have been added, removed, or modified should not be considered unchanged.

## Why This Matters

Without this behavior, trigger-aware caches can become stale after a flow update that only modifies tasks. The system should consistently keep all relevant components in sync whenever a flow revision changes, regardless of whether the triggers themselves were modified.
