## Description

The SDK currently has no way to send encrypted messages directly to specific devices outside of a room context. This missing capability is particularly relevant for real-time communication use cases (like distributing session keys for audio/video calls) where a client needs to send arbitrary encrypted payloads to a known set of target devices without going through the standard room message flow.

## Expected Behavior

- A client should be able to encrypt arbitrary content and send it directly to a list of specific devices in a single API call, using device-to-device (Olm) encryption.
- The operation should be exposed as an experimental feature that can be opted into via a compile-time feature flag, since the API may evolve.
- When some devices cannot receive the message — either because an encrypted session could not be established (e.g., one-time keys are exhausted) or because the server rejects delivery — those devices should be reported back to the caller as a list of failures rather than causing the entire operation to fail with an error.
- The caller should be able to inspect which user and device combinations failed, so it can handle partial failures appropriately.

## Why This Matters

Without this capability, clients cannot implement device-to-device encrypted messaging for cases like real-time media key distribution. The graceful failure reporting (returning per-device failures instead of a top-level error) is important so that a failure to reach one device does not block the content from being delivered to others.
