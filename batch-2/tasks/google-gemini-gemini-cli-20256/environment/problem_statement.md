## Description

The CLI has two related but disconnected commands for session management: one opens an interactive browser for auto-saved conversations, and the other contains subcommands for manually managing named conversation checkpoints (listing, saving, resuming, deleting, and sharing). Currently these are completely separate, so users must remember two different command names for related operations. This is confusing because both commands are logically about managing conversation sessions.

Additionally, the error messages in the checkpoint subcommands currently reference the wrong command name, and the chat command's description doesn't accurately describe its purpose now that it should also open the session browser.

## Expected Behavior

- The session browser command should directly expose the same checkpoint management subcommands (list, save, resume, delete, share), so users can use either command interchangeably
- Both commands should produce the same grouped autocomplete menu with visual section separators — one section for auto-saved sessions and one for manual checkpoints
- Typing a unique partial prefix of either command should immediately show the same grouped menu, not wait for an exact match
- A hidden compatibility alias under the session browser command should preserve the old nested structure for any existing workflows
- Error messages in the checkpoint subcommands must reference the session browser command name, not the chat command name
- The chat command description must be updated to reflect that it now also opens the session browser and manages checkpoints
- The autocomplete suggestion system must support inserting a canonical command form (different from the suggestion's display label) when a suggestion is selected
- In nightly builds, the debug subcommand must be added to both commands and their nested checkpoint aliases

## Why This Matters

This unification removes a confusing split between two commands that do similar things. Users can now use the session browser command as a one-stop entry point for all session-related operations, and the autocomplete experience consistently guides them to the right subcommands with clear visual grouping.
