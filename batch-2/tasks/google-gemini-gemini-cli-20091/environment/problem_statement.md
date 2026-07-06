## Description

The current remote agent communication system uses a request-response model: it sends a message and waits for the entire response before displaying anything. This means users see no feedback during potentially long agent operations. We need to upgrade this to support streaming so that incremental responses can be shown as they arrive.

## Expected Behavior

- The agent communication layer should expose a streaming interface that yields results incrementally rather than returning a single complete response.
- A utility for determining whether a task has reached a terminal state (completed, failed, canceled, or rejected) should be available.
- The response ID extraction utility should indicate whether the task ID should be cleared after a terminal response, allowing subsequent interactions to start fresh tasks.
- A reassembler utility should exist that can accept a sequence of streaming event chunks and build a coherent, formatted text output. It should handle status messages and artifact updates (including incremental artifact appends), joining sections with blank lines and labeling artifact sections by name.
- The remote invocation layer should iterate the stream, progressively notify a provided callback with the growing assembled output, and properly handle abort signals mid-stream — resolving with an error rather than hanging or crashing.

## Why This Matters

Without streaming, users interacting with remote agents have no visibility into progress and must wait for the full response before seeing any output. This change enables responsive, progressive UX where output appears as the remote agent produces it. It also ensures that cancellation during a streaming operation is handled gracefully, rather than leaving the system in an undefined state.
