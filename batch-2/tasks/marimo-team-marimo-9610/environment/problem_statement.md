## Description

The WebSocket reconnection and health-check infrastructure has a few design issues that are causing coupling between layers and making the code harder to reason about.

**Retry budget tracking is spread across too many layers.** Currently, the low-level transport and the higher-level close-event classifier both need to know about retry counts. When the transport runs out of reconnection attempts, the classifier has to receive the retry count as an input to decide whether to give up. This coupling means the classifier can't stand alone — it always needs external state injected into it. Instead, the transport itself should detect when its retry budget is exhausted and signal that fact with a clear, named close reason. The classifier can then handle that named signal as a terminal case without needing any retry count.

**The health-check method does two unrelated things.** The current single health-check method both verifies that the backend is reachable and, on receiving a redirect response, updates the runtime's base URL. These are distinct concerns: sometimes a caller just wants to know if the server is up, without any URL state being mutated. Other callers need the full reconciliation behavior. Mixing these into one method causes unintended side effects when a simple read-only probe is needed.

## Expected Behavior

- The low-level transport layer should handle retry budget exhaustion internally and emit a specific named signal when exhausted, rather than relying on callers to pass retry counts to downstream classifiers.
- The close-event classifier should accept only a close event (with a reason string), not a retry count, and should treat the exhaustion signal as a terminal "give up" case.
- The health-check functionality should be split into two methods: one that purely probes health without side effects, and one that also reconciles the runtime URL on redirect.
- The transport layer should deduplicate event listener registrations and fully clean up on removal.

## Why This Matters

These changes make the reconnection logic cleaner and more predictable, reduce hidden coupling between layers, and make health checking safer by preventing unintended URL mutations when only a simple probe is needed.
