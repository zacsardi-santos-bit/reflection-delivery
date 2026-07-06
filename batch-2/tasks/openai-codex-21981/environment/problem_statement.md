## Description

Thread listings currently use the first user-typed message as the preview text shown in discovery and list views. This works for interactive sessions where a user opens a conversation and types something. However, agentic or programmatic sessions often begin with an automated goal — an objective set before any user interaction occurs — and never produce a first user message at all. As a result, these goal-initiated sessions either appear invisible in thread lists or show empty previews, making them impossible to identify or navigate back to.

## Expected Behavior

- When a session is initiated with a goal objective, the objective text should serve as the preview for that thread in listings.
- If a user later sends a message in a goal-initiated session, their message should be recorded separately as the first user message, while the goal objective remains the thread's preview.
- For ordinary interactive sessions (no goal), the preview and first user message should remain the same (the user's opening message).
- Threads with no discoverable preview — neither a goal objective nor a user message — should continue to be excluded from listings.

## Why This Matters

Teams using agentic workflows rely on the thread list to manage and revisit ongoing or completed sessions. Without this fix, all goal-driven sessions are invisible in the UI, making it impossible to track or resume agentic runs. Surfacing the goal objective as the preview gives users a meaningful label for every session regardless of how it was started.
