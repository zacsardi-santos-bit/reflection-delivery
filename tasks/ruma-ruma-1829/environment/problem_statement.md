## Description

The MatrixRTC call membership state event type needs to be extended to support three distinct formats that the protocol now requires, but currently only handles one. At present, the event content is a simple struct that only knows how to represent a user's sessions as a flat array. The protocol has evolved to also require a per-device format (one event per device rather than one event for all devices of a user) and an empty/departed state (indicating that a user has left the session entirely). Because the code only supports the old array-based format, it cannot compile when code using the new formats is present, causing all tests in the crate to fail.

Additionally, the call notification event — which lets users be notified of incoming MatrixRTC calls — has its tests gated behind an optional experimental feature flag in the integration test file. Now that the feature is more mature, those tests should live inline in the module's own test block, so they run alongside the rest of the feature's code.

## Expected Behavior

- The call membership event content type should be a union/enum that covers all three membership states: the legacy array-of-sessions format, the new per-device session format, and an empty departure state.
- Each variant should serialize and deserialize correctly to and from its respective JSON representation.
- Session-format memberships should never be considered expired; legacy-format memberships should expire based on their duration and creation timestamp.
- A complete state event of the call membership type should parse successfully from JSON, including correctly identifying the empty departure state when the content is an empty object.
- The notification event tests should be moved into the module itself and no longer require a separate feature flag guard.
- The call notification feature should declare the MatrixRTC membership feature as a required dependency.

## Why This Matters

Without this fix, any code that mixes the legacy and new membership formats causes a compilation failure. Clients implementing the newer per-device MatrixRTC session format cannot use the library until both formats are supported. The empty departure state is also necessary for reliably signaling that a user is no longer in a call when their client becomes unreachable.
