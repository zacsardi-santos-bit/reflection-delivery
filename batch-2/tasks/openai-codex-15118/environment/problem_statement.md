## Description

Lifecycle hooks currently receive context about the active session (session ID, transcript path, current working directory, model, etc.) but nothing that identifies which conversation turn is active. This makes it impossible for hook scripts to correlate multiple invocations that belong to the same turn, or to distinguish events from different turns in a multi-turn session.

## Expected Behavior

- The stop hook input should include a turn identifier so that hook scripts can know which turn triggered the stop event.
- The user-prompt-submit hook input should include the same kind of turn identifier.
- When the same turn triggers multiple hook invocations (e.g., a stop hook that fires several times in one turn, or several prompts submitted during the same turn), all those invocations should carry the same turn identifier.
- The turn identifier should be a non-empty string so that hook scripts can reliably use it as a correlation key.
- Both blocked and accepted user prompts should trigger the user-prompt-submit hook, each carrying the correct prompt text and the turn identifier.

## Why This Matters

Without a turn identifier, hook scripts have no reliable way to group related hook events together. Adding this field makes it possible to build integrations that understand the conversation's turn structure — for example, logging or auditing systems that need to associate stop events and prompt submissions with specific turns.
