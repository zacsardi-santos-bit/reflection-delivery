## Description

The Python SDK currently lacks the infrastructure needed to subscribe to and consume real-time event streams from LangGraph threads using the new streaming protocol. There is no client-side abstraction for opening a persistent server-sent event connection, receiving filtered events, or managing multiple independent consumers of the same event stream.

## Expected Behavior

- Developers should be able to open an event stream to a running thread and receive events in real time over SSE.
- Late-joining consumers should automatically be replayed all events buffered since the stream opened, starting from the beginning — not just events that arrive after they subscribe.
- Multiple independent consumers should each receive the full event sequence without interfering with one another.
- The streaming layer should support backpressure: slow consumers should not cause unbounded memory growth; the pump should suspend until the consumer catches up.
- Events should be filterable by channel type and namespace depth, so a subscriber can opt in to only the events it cares about.
- Mid-stream transport errors should be surfaced explicitly to the caller, distinguishable from a clean stream end.
- Canceling or closing a stream should stop event delivery immediately without spurious post-cancel events leaking out.
- Sending commands to a thread (e.g., to start a run) should work alongside streaming, with proper error handling for HTTP error responses and empty response bodies.

## Why This Matters

Without this streaming layer, the SDK cannot participate in the new protocol for real-time thread event delivery. Adding it allows SDK consumers to build applications that react to agent lifecycle events, checkpoints, tool calls, and custom events as they happen, with reliable ordering and subscription semantics.
