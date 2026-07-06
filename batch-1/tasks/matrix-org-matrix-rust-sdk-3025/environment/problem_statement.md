## Description

There are two separate but related improvements needed for the timeline and room list service:

### 1. Explicit timeline initialization

Currently, accessing a room's timeline through the room list service automatically initializes it as a side effect of that single call. This means callers have no opportunity to configure the timeline before it starts — there's no hook to provide custom settings, builders, or initialization parameters. We need to separate timeline initialization from timeline access so that callers can explicitly initialize the timeline with a builder before retrieving it.

### 2. Declarative event-type filter

Today, filtering timeline events requires writing a custom predicate closure. For the very common case of wanting to include or exclude specific categories of events (e.g. "show only room name changes" or "hide all messages"), this is overly verbose. We need a convenience filter type that lets callers express include/exclude lists of event types declaratively, without writing custom logic.

## Expected Behavior

- Accessing a room's timeline via the room list service should require explicit prior initialization using a builder; the access method should return nothing (or fail gracefully) if initialization has not been performed.
- A room should provide a method that returns a default builder, and a separate async method that accepts any builder and uses it to initialize the timeline.
- A new filter type should exist that supports two modes: a whitelist mode (only listed event types appear in the timeline) and a blacklist mode (listed event types are hidden from the timeline).
- This filter type should be usable in the existing event filter configuration for the timeline.

## Why This Matters

These changes make it possible to configure timelines properly before use and to build focused, selective timeline views (such as an audit log showing only specific event categories) without boilerplate.
