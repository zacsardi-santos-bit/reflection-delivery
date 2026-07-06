## Description

Several data structures in the realtime conversation system use a field named "session ID" that is ambiguous — it's not immediately clear whether it refers to the realtime session, a conversation thread, a user session, or some other session-like entity in the system. This naming ambiguity makes the API harder to understand at a glance, especially for developers who work across multiple session concepts.

## Expected Behavior

The field should be renamed to make it unambiguous that it refers specifically to the realtime session identifier, not any other kind of session or thread-group identifier in the system. This rename should be reflected consistently across all data structures that carry this identifier:

- The parameters used to start a realtime conversation
- The notification emitted when a realtime conversation starts
- The event type that signals a realtime session has been updated

## Why This Matters

Developers working with the realtime conversation APIs should be able to tell at a glance what kind of session identifier a field holds. With the current generic name, it is easy to mistakenly assume it maps to a Codex session or thread-group identifier, when in fact it refers to the upstream realtime session. A more precise name reduces the chance of confusion, improves code readability, and aligns the field name with what it actually represents.
