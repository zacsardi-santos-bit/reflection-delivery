## Description

When peers on the network send transaction announcements, the node currently accepts those announcements without validating their contents. This means a node may waste resources attempting to fetch transactions that were announced with invalid or nonsensical metadata — such as unrecognized transaction types, zero-byte encoded sizes, duplicate hashes, or completely empty messages.

We need a validation layer that sits between receiving an announcement and dispatching fetch requests. This layer should inspect each entry in an announcement and decide whether to fetch it, silently ignore it, or flag the announcing peer for bad behavior.

## Expected Behavior

- An empty announcement (zero entries) should immediately flag the sending peer for a reputation penalty.
- Announcement entries with unrecognized transaction types should be filtered out and the peer should be penalized.
- Announcement entries with encoded sizes below an absolute minimum (zero bytes) should be filtered out, but the peer should **not** be penalized — this may be an honest mistake.
- Announcements containing duplicate transaction hashes should be deduplicated and the peer should be penalized.
- Valid entries from an announcement should be returned in a map structure that also carries per-entry metadata (transaction type and size) for the newer announcement protocol version, and simple hash-keyed entries with no metadata for the older protocol version.
- The filter outcome must clearly signal whether the caller should apply a reputation change to the peer.

## Why This Matters

Without this validation step, a misbehaving peer could flood the node with garbage announcements. This change improves resilience to bad actors while remaining lenient toward honest peers who may send benign but slightly malformed data.
