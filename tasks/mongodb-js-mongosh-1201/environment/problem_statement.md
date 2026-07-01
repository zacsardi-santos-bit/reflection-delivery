# Decouple Telemetry Gating from Individual Analytics Events

## Description

Currently, the analytics/telemetry system passes a boolean flag along with each bus event to indicate whether telemetry is currently enabled. This means every event handler has to check this flag individually, and there is no way to buffer events while waiting for the user's telemetry preference to be determined. Additionally, the analytics object is provided via a factory function whose failure had to be caught and handled as a special case.

We need a cleaner design where the analytics object itself is responsible for deciding when to forward events — buffering them when the preference is unknown, flushing or discarding them when the preference is resolved, and allowing further toggling after that.

## Expected Behavior

- The logging/telemetry setup function should accept an analytics object directly, rather than a factory function that produces one. Removing the factory indirection also removes the need for special error-handling around analytics creation.
- The bus events that notify about user identity and user updates should no longer carry a telemetry-enabled flag. The telemetry on/off decision should be made externally and should not be part of individual event payloads.
- A new analytics wrapper type should be introduced that starts in a paused (buffering) state. It should support three transitions: enabling (which flushes buffered events and starts forwarding), pausing (which buffers new events without forwarding), and disabling (which discards buffered events so they are never forwarded even if the wrapper is later re-enabled).
- When the shell loads a global configuration file, this event should be logged with enough detail (the file path) to make configuration troubleshooting straightforward.

## Why This Matters

These changes make it possible to implement telemetry behavior where events are only sent after the user's telemetry preference has been confirmed, while correctly discarding them if the user opts out before any events are flushed. The simpler function signature also makes the API easier to use correctly.
