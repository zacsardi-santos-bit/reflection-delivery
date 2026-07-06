## Description

When building agents that perform complex, multi-step tasks, there is no built-in way for the agent to track its own work items across a session. Agents need to be able to break down complex requests into smaller pieces, keep track of what has been done, and remember what still needs to be completed — even across multiple turns of conversation. Without a standard component for this, developers must build their own task-tracking logic from scratch for every agent.

## Expected Behavior

- The framework should provide a reusable AI context provider that equips agents with a full set of todo list management capabilities.
- The provider must expose tools that allow agents to: add new items (with a title and optional description), mark items as complete by their ID, remove items by their ID, retrieve only the remaining incomplete items, and retrieve all items.
- Each new item must receive a unique, auto-incrementing numeric ID starting at 1.
- The todo list state must persist across multiple agent invocations within the same session.
- The provider must also expose public methods so that host application code can read the current list of all items or only incomplete items directly from a given session, without going through the AI tools.
- Querying todos from a session that has never had items must return an empty list without error.

## Why This Matters

Agents tackling open-ended, multi-step tasks need a structured way to plan their work, track progress, and clean up completed or irrelevant items. A reusable built-in provider removes the need for every developer to implement this pattern themselves and ensures consistent behavior across sessions.
