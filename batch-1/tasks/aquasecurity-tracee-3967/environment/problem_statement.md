## Description

The event dependencies manager handles activation and deactivation of kernel tracing events and their transitive dependencies. Currently it has several limitations that make it fragile in production use:

1. **No probe dependency tracking.** Events depend not just on other events but also on specific kernel probes. The manager tracks event-to-event dependencies but has no concept of event-to-probe dependencies. There is no way to determine which probes are still in use when events are removed, making clean probe lifecycle management impossible.

2. **No cancellation support for event activation.** When an event is activated and one of its dependencies cannot be satisfied (e.g., a required kernel symbol is missing or a probe cannot be attached), there is no mechanism to abort and roll back the partial addition. The result is an inconsistent state where some events are activated but their dependencies cannot be met.

3. **Boolean returns instead of errors for lookups.** Event and probe lookup methods return a boolean to indicate success or failure. This makes it impossible for callers to distinguish between "not found" and other failure modes, and it is inconsistent with Go conventions for operations that can fail.

4. **Subscriber callbacks are passive observers.** Callbacks registered for node additions and removals are fire-and-forget — they cannot influence the outcome of the operation they observe. There is no way for a subscriber to veto an event addition.

## Expected Behavior

- The manager should track which kernel probes each event depends on. A probe lookup method should return the probe node and which events currently depend on it.
- Event activation should return an error. Subscribers should be able to return a cancel signal, causing the activation to roll back all events added during that call and return a distinct error type.
- Event lookup, probe lookup, and event removal methods should return errors using a sentinel "not found" value instead of booleans, making error inspection straightforward.
- When an event is removed and a probe is no longer needed by any remaining event, that probe should be cleaned up from the manager.
- Subscriber callbacks should return a slice of actions rather than being void, allowing them to influence the outcome of node additions.

## Why This Matters

These improvements are needed to support graceful degradation: if an event cannot be fully loaded (due to a missing kernel symbol or a probe attachment failure), the system should detect this, log a clear message, and remove the event cleanly — leaving the rest of the tracing pipeline unaffected. Without probe tracking and cancellation support, failed event activations leave the system in a partially-initialized state with no clear path to recovery.
