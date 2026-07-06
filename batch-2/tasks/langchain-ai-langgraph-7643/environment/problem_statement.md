## Description

When consuming multiple output channels from a graph run simultaneously, items from different channels are currently interleaved in a round-robin rotation. This means that if one channel produces many items before another produces any, those items are artificially delayed — held back until the rotation reaches them — rather than being delivered in the order they actually arrived.

Additionally, after finishing a combined multi-channel iteration, channel subscriptions are not released. This blocks any downstream observer from seeing that the channels have finished, since they remain marked as subscribed indefinitely.

## Expected Behavior

- Items from multiple channels merged into a single sequence should appear in the exact order they were produced, not interleaved by rotation.
- If one channel produces three events before another produces one, those three events should all appear first in the combined output.
- After multi-channel iteration completes — including early cancellation — all channel subscriptions must be released so that observers can detect the completed state.
- If iteration is interrupted by an error, subscriptions must still be cleaned up.
- Attempting to start combined iteration over a channel that is already being consumed should raise an error indicating the channel already has a subscriber.

## Why This Matters

Consumers of graph run streams that combine multiple output types (e.g., state values and messages) need the events in time order so they can display or process them correctly. Round-robin interleaving breaks this guarantee and makes the API harder to reason about. Leaking subscriptions also makes it impossible for callers to reuse or inspect channels after a stream completes.
