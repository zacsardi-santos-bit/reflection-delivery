## Description

The context memory subsystem currently uses a flat, free-text approach to summarize conversation history. When the system needs to compress older turns to free up context space, it asks the model to produce a prose summary — but this summary cannot be incrementally updated. Every new compression cycle re-summarizes everything from scratch, which makes it impossible to track specific facts, active tasks, or user preferences in a durable, structured way. Important details like file paths, error codes, and user instructions can easily be lost or overwritten.

## Expected Behavior

- The memory snapshot should be a structured document with distinct sections: active tasks (each with a unique identifier and description), discovered facts (a list of empirical strings), user constraints and preferences, and a rolling chronological summary.
- New information extracted from each new batch of conversation turns should be merged into the existing state, not replace it. Facts can be appended or removed by index; tasks can be added or removed by ID; and the chronological summary should operate as a rolling window.
- If the model call fails or returns malformed data, the existing state must be returned unmodified — no data loss.
- The system must enforce a configurable token budget on the resulting state. When the state exceeds the budget, it should prune data in priority order: oldest facts first, then oldest constraints, then oldest narrative summaries, then tasks as a last resort.
- When preparing context for the model, conversation nodes should be formatted with semantic labels and relative turn indices. Tool responses should include a semantic wrapper indicating the tool category (e.g., shell execution, file content). Large tool responses should be truncated with a clear marker so they do not overwhelm the context window.
- Both the synchronous and asynchronous memory processors should scan existing context nodes for a prior snapshot to use as a starting baseline, even when the normal coordination channel is empty. This "global lookback" ensures memory is cumulative.
- When a new snapshot is produced, any prior baseline snapshot it absorbed must be tracked in its list of consumed node identifiers so those nodes are properly garbage collected.

## Why This Matters

Without structured, incremental memory updates, the agent loses continuity across longer sessions. Specific facts, in-progress tasks, and user preferences are silently discarded every time context is compressed. This change makes the agent's memory persistent, auditable, and safe to update without risking data loss.
