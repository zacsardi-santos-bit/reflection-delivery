## Description

We need a standardized way to take a snapshot of a collaborative document's full live state and serialize it to a portable format, and then reconstruct an equivalent document from that snapshot. Right now, there is no built-in mechanism for this: you can't easily export a document's current CRDT state — including all its complex data types like counters, rich text, and trees — serialize everything to a compact JSON string, and recreate an identical document from that serialized form.

Additionally, the current API for initializing a tree data type in a document is inconsistent: it requires passing a reference to the initial node, whereas the rest of the API works with values. This inconsistency causes friction and subtle bugs.

## Expected Behavior

- Developers can convert a live document's root state into a typed, JSON-serializable snapshot that preserves all CRDT type information (objects, arrays, counters, text, trees, and primitives).
- The snapshot can be serialized to a compact JSON string format.
- The serialized snapshot can be deserialized back into the same typed representation.
- A document can be reconstructed from a snapshot such that its serialized form is identical to the original.
- The tree initialization API accepts a value instead of a reference to the initial node, consistent with the rest of the API.

## Why This Matters

This enables use cases like state transfer, offline sync, document storage, and debugging — all of which require the ability to export a document's current state to an external representation and reimport it without losing type fidelity.
