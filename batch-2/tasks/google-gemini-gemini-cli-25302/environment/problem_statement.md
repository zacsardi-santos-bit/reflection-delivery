## Description

The agent system supports multiple protocol implementations for running subagents, but there is currently no protocol that wraps a locally-executing subagent and exposes it through the standard event-based interface used across the rest of the system. This means locally-defined subagents cannot participate in the same streaming event model as other agent types, and callers have no standardized way to observe their progress, retrieve their results, or cancel them mid-flight.

## Expected Behavior

A new session class should bridge the gap between a local agent executor and the agent event system:

- Callers can subscribe to a stream of structured events representing the agent's lifecycle (start, end), thinking activity, tool invocations, and errors.
- Input can be sent either as configuration updates (buffered until a message is sent) or as a direct message. Multiple configuration updates accumulate and are merged with the next message.
- When message text is absent, no query field should be included in the executor input.
- Configuration buffered for one interaction must not carry over to the next.
- A result accessor provides the raw executor output once execution completes, and rejects appropriately when the executor fails.
- Calling the result accessor before sending any input must produce a clear error.
- Execution can be aborted; after abort the result accessor resolves with an empty result rather than rejecting.
- The session guards against concurrent message sends while a stream is active.
- Sequential interactions after a stream completes are supported, each producing a fresh event stream with its own lifecycle events and a distinct identifier.
- Each stream's events (start, activities, end) all share the same identifier, and start/end lifecycle events are emitted exactly once per stream even under error conditions.
- Activity events from the local executor are translated into the typed agent event format: thought chunks become message events with thought content, tool call starts become tool request events, tool call ends become tool response events, and errors become non-fatal error events with an internal status. Unknown activity types produce no events.
- Terminate modes from the executor map to standardized stream end reasons: successful completion maps to "completed", timeout to a time-exceeded reason, turn limit to a turn-exceeded reason, abort to "aborted", and error conditions to "failed".
- An optional raw activity callback, provided at construction time, fires with the unprocessed activity data before any translation, allowing callers to inspect or forward the original events.
- Multiple subscribers can observe the same event stream simultaneously, and each subscriber can independently unsubscribe.

## Why This Matters

Without this bridge, locally-defined subagents are isolated from the rest of the infrastructure and cannot be composed with other agent types, monitored through a consistent event interface, or managed (started, observed, cancelled) in a uniform way. This addition completes the protocol layer so local subagents are first-class participants in the agent event system.
