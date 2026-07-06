## Description

When using the TUI with a model that isn't recognized, the application displays a fallback metadata warning in the conversation history. The problem is that this warning reappears every single time the model is used within the same session — even though the user has already been informed. This creates repetitive, noisy output in the chat history that obscures actual conversation content.

## Expected Behavior

- When the same model-specific fallback warning has already been shown in the current session, subsequent occurrences of that exact warning for the same model should be silently suppressed.
- The first occurrence of a model-specific warning should still appear normally and must include the model's identifier in the displayed text so the user knows which model triggered it.
- Generic (non-model-specific) warning messages should not be affected by any deduplication — each occurrence should continue appearing in history as usual.

## Why This Matters

Users working with an unrecognized model in a long session are currently bombarded with the same fallback warning on every turn, making the conversation history hard to read. Deduplicating these model-specific warnings so they appear only once per session significantly reduces noise while still ensuring the user is informed the first time the issue occurs.
