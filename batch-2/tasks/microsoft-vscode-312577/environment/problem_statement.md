## Description

The list of available deferred tools is currently embedded inside the tool search instruction block, which is part of the system prompt. This is problematic because the system prompt is often cached across conversation turns — meaning the tool list embedded there may not accurately reflect the tools available in the current session. If a new tool becomes available or the set of deferred tools changes, the stale snapshot in the cached system prompt means the AI may not be aware of the current state.

## Expected Behavior

- The deferred tools inventory should be moved out of the tool search instructions and into the initial conversation context, so that it is rendered fresh at the start of each new conversation.
- The instruction text that references the tool list's location should be updated to reflect the new placement (i.e., it is "provided in the initial conversation context") rather than referring to it as being "below" in the instructions.
- References to the list such as "listed below" and "above" in related instruction blocks should be removed or updated to avoid confusion.
- The system prompt tool search instructions should only contain guidance on how to search for tools — not the actual list of tools.

## Why This Matters

Separating the deferred tool inventory from the cached system prompt ensures the AI always sees an accurate, up-to-date snapshot of available tools at the start of each session. This avoids scenarios where the AI is guided by a stale tool list. It also keeps the system prompt more stable and cache-friendly, since the tool list (which can change) no longer invalidates the cached system instructions.
