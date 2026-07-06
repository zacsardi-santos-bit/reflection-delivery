## Description

Multi-turn conversations fail when the model API is used in stateless mode (where the server does not persist responses between turns). When building the conversation history to send on subsequent turns, the current code assumes the server has stored all prior responses and can resolve them by ID. In stateless mode, those IDs don't exist on the server, so the replayed history includes unresolvable references—causing failures.

## Expected Behavior

When stateless operation is configured, the conversation history builder should:

- Omit assistant message IDs, since the server can't look them up
- Drop any reasoning steps that are bare references (the server can't replay them without storage), but keep reasoning items that carry their own self-contained encrypted context
- Drop image generation call references that have no image data attached; keep ones that carry the actual result, but strip positional index metadata that isn't needed for replay
- Keep tool call items intact, including their IDs (needed to match tool responses)

In stateful mode (the default), all existing behavior should remain unchanged: message IDs and reasoning blocks are preserved as before.

## Why This Matters

Without this fix, developers using stateless mode for multi-turn conversations see failures because the replayed history references server-side items that don't exist in stateless operation. This makes stateless multi-turn conversations unreliable. With this fix, the library correctly handles both stateful and stateless modes, enabling robust multi-turn interactions regardless of whether server-side storage is enabled.
