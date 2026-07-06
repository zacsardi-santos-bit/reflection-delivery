## Description

When a user manually exits the read-only planning mode by switching to a different approval mode, the AI model has no way of knowing this context switch happened. Nothing is injected into the conversation history to communicate whether a plan was approved and execution should proceed, or whether the user decided to exit planning manually without approving. This makes it harder for the AI to understand the current state of the conversation.

## Expected Behavior

- When a user manually exits planning mode (by explicitly selecting a different approval mode), a notification message should be automatically added to the conversation history explaining that the user has manually exited planning mode and indicating which mode they switched to.
- When the switch away from planning mode happens automatically (e.g., after a plan is approved), the notification message should reflect that a plan was approved and indicate which mode is now active.
- A utility should exist to generate consistent, human-readable descriptions for each available approval mode.

## Why This Matters

Without this notification, the AI model receives no signal about why the mode changed or what the user intends to do next. Adding a contextual message to the conversation history ensures the model understands the transition — whether the plan was approved for execution or the user chose to exit planning manually — enabling better continuity in the conversation.
