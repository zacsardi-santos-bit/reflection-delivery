## Description

When a marimo notebook file is reloaded from disk, the current implementation replaces the entire internal app object with a freshly loaded one. This means any code that was holding a reference to the cell manager, the notebook document, or the compiled cells before the reload ends up pointing to stale, abandoned objects — the post-reload objects are completely new instances. This breaks any consumer that expects those references to stay stable across a reload (e.g. a session that captured the document before calling reload).

Additionally, the reload operation currently returns only a set of changed cell IDs. Callers that need to broadcast the diff to other consumers (e.g. connected clients) have to reconstruct the transaction themselves from a snapshot taken before the reload, which is error-prone and creates a two-step, race-prone process.

## Expected Behavior

- Reloading a notebook file should update the existing data structures in-place rather than swapping them for fresh objects. After a reload, the cell manager, the document, and their internal state should be the same objects they were before — only their contents should change.
- The document's version counter should advance monotonically with each reload, staying consistent with normal edit-based version increments.
- The reload method should return both the transaction describing the diff and the set of changed cell IDs together, so callers receive everything they need in one call without having to snapshot state beforehand.
- File change coordination should rely on the reload operation's return value directly rather than rebuilding the diff separately.

## Why This Matters

Code that holds references to the notebook document or cell manager — such as a session object that tracks the current document — should not have those references silently invalidated whenever the underlying file changes on disk. Making reload an in-place mutation rather than a full replacement eliminates this subtle source of bugs and makes the reload path consistent with how normal edits update the document.
