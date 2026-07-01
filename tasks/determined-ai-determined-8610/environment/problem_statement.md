## Description

The streaming system's test infrastructure has a bug that prevents reliable integration testing of several important real-time update scenarios. Specifically, the message validation helper used to verify streaming behavior contains two related defects:

1. When sorting deletion messages for comparison, the code mistakenly uses the upsert slice as the comparator instead of the deletion slice. This causes incorrect results whenever expected deletions are sorted, meaning tests that check deletion behavior can silently produce wrong outcomes.
2. The sorting code runs unconditionally on all slices, including those with zero or one elements. Sorting a nil or single-element slice with the current logic can cause a panic or incorrect behavior.

Because of these bugs, it has not been possible to write reliable integration tests for the following streaming scenarios:

- **Updates outside subscription scope**: When a database update affects an entity that is not covered by a client's subscription, the streamer should silently ignore it. The subscriber should receive updates only for entities within their subscription.
- **Offline catch-up (fall-in)**: When a client reconnects after being offline and entities that now fall within its subscription were created while it was gone, the reconnection sync should deliver those missed upserts.
- **Offline catch-up (fall-out)**: When a client reconnects and entities it previously knew about were deleted while it was offline, the reconnection sync should deliver deletion notifications listing those removed IDs.
- **Online scope changes**: When an entity moves into or out of a subscription's scope in real time (e.g., because its parent object changes), the streamer should immediately send the appropriate upsert or deletion to the active subscriber.

## Expected Behavior

- The sorting fix ensures that deletion messages are sorted using the correct comparator.
- Sorting is only applied when a slice has more than one element, preventing crashes on empty or single-element slices.
- Integration tests for all four scenarios above pass reliably.

## Why This Matters

Without these fixes, test results for subscription-scoped streaming were silently incorrect or would panic, making it impossible to verify the correctness of the real-time update delivery system for edge cases involving out-of-scope updates, reconnection catch-up, and live scope transitions.
