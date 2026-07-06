## Description

The chat interface currently displays notifications for hooks that fire during AI tool use, but it does not support a new category of hook that fires before a user's prompt is submitted to the AI. These "user prompt submit" hooks can inspect the user's input, produce warnings, or block the submission entirely. The protocol and rendering layer need to be updated to support this new hook event type, as well as new output entry categories (warnings and stops) that such hooks can produce.

Additionally, when a session is refreshed or rebased, hook notifications are currently not preserved. This means users lose visibility into which hooks ran during their conversation. Hook notifications (both started and completed) should survive a session rebase so they remain visible in conversation history.

## Expected Behavior

- The app-server protocol must include a new hook event type for user prompt submission, alongside new output entry categories for warning and stop outcomes.
- When a user prompt submit hook starts, the chat UI should display an inline notification showing that the hook is running and what it is checking.
- When such a hook completes — whether it produced warnings, blocked the prompt, or both — the chat UI should show the hook's final status and each output entry (warning or stop) with its message.
- Conversion from app-server protocol hook types to core protocol hook types should be done via direct field mapping rather than an intermediate conversion process, so that new variants and fields are not silently dropped.
- After a session refresh or rebase, hook started and completed notifications must remain in the event store so they continue to appear in the conversation.

## Why This Matters

Users who configure hooks to validate or gate their prompt submissions need visual feedback in the chat UI. Without this, they have no way to know whether their hook ran, what it found, or whether it blocked their input. Preserving hook notifications through session rebase ensures that this history is not lost when the session state is refreshed.
