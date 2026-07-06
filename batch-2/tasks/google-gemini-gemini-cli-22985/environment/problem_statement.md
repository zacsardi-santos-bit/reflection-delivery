## Description

The agent runtime processes a continuous stream of events from the underlying model API, but the logic for translating those low-level stream events into the structured, typed events consumed by the rest of the system has not been extracted into a standalone, independently testable module. As a result, this translation layer is hard to verify in isolation and the event types themselves don't take full advantage of language-level type narrowing.

## Expected Behavior

- A dedicated translation module should exist that converts each kind of raw model stream event into the appropriate structured agent events. This includes text content, thoughts, tool call requests and responses, model information updates, error conditions, session lifecycle events (start, end, cancellation), usage statistics, and more.
- The module should maintain per-stream state so that, for example, tool call responses can be matched to their originating requests by ID.
- Each translated event should carry a unique ID (scoped to its stream) and the stream identifier.
- Error events in the stream should be mapped to a consistent status classification (distinguishing authentication errors, rate limits, general failures, etc.).
- The structured event type for agent events should be a proper discriminated union, so that code checking an event's type can access type-specific fields without requiring explicit type casts.

## Why This Matters

Having a well-tested, isolated translation layer makes it far easier to verify correctness of the event pipeline, to add new event types in the future, and to ensure that type narrowing works naturally throughout the codebase — reducing both bugs and boilerplate.
