## Description

The checkpoint system in LangGraph currently only allows retrieving saved checkpoints by their exact configuration (thread ID and timestamp). There is no way to search across all saved checkpoints based on their associated metadata. This makes it impossible to, for example, find all checkpoints that represent user-approved or high-quality interactions.

We need to add:
1. A **search capability** to checkpoint backends (SQLite, async SQLite, and in-memory) that allows filtering checkpoints by any combination of metadata fields.
2. A **score field** on checkpoint metadata, so developers can mark individual checkpoints as high-quality.
3. A **few-shot examples managed value** that automatically retrieves well-scored past checkpoints and injects them as dynamic context into subsequent graph invocations.

## Expected Behavior

- Checkpoint backends must expose synchronous and asynchronous search methods that accept a metadata filter dictionary and return matching checkpoints.
- Searching with an empty filter returns all checkpoints. Searching with one or more key/value pairs returns only checkpoints whose metadata contains all matching entries. Searching with a filter that matches nothing returns an empty result.
- A new optional score field on checkpoint metadata allows callers to attach an integer quality rating to any checkpoint.
- A new managed value type, when declared as a graph state field, automatically queries the checkpointer for highly-scored past checkpoints and makes them available as examples during each graph run. The number of examples can be configured. On the first run (when no checkpoints have been scored), the examples list is empty.

## Why This Matters

This enables a retrieval-augmented few-shot prompting pattern: developers can mark good past conversations as high-quality, and future runs of the same graph will automatically receive those examples as in-context demonstrations. This improves response quality without requiring any manual prompt engineering between runs.
