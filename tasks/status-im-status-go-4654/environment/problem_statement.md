## Description

The wallet activity session feed currently notifies clients about new pending transactions by immediately embedding the full entry data inside each session update event. This approach makes it difficult for clients to know which entries in a full activity list are truly "new" (arrived since the last client-acknowledged state) versus pre-existing entries.

We need a revised flow:
- When new activity is detected in a session (e.g., a new pending transaction appears), the session update notification should only signal that new entries exist — not carry the full entry data.
- Clients should then be able to request a reset of the session to fetch the full updated activity page, and the response must explicitly mark each entry as new or not new so the UI can highlight them.

## Expected Behavior

- When the system detects new activity entries for an active session, it emits a session update event that carries a flag indicating whether new entries are present, rather than including the full entry data.
- When a client resets the session filter, the returned activity list includes all entries for the requested page, with each entry annotated to indicate whether it is a freshly arrived entry or a pre-existing one.
- The utility function that computes differences between a known list of entry identities and a new set of entries should correctly return which entries are new (and their positions) and which have been removed.
- The function for generating test pending transactions should support a configurable starting offset, allowing generated transactions to start from a given index rather than always starting at zero.

## Why This Matters

Without this change, the UI has no reliable way to highlight newly arrived transactions after a session has already been loaded. The two-phase model (signal + reset) makes it possible to batch updates and clearly communicate which items are new when the client refreshes.
