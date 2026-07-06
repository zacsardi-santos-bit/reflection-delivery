## Description

There are two related improvements needed in the agent integration layer.

**1. Surface intermediate agent messages as activity items**

When a remote agent processes a task, it sends intermediate status messages during execution (e.g., "working" state with a message). Currently, these in-progress messages are tracked internally but there is no way to retrieve them in a structured format for display or downstream processing. We need a way to retrieve all accumulated agent messages as a list of activity items, where each item has an identifier, a type indicating it represents the agent's reasoning/thought process, the message content, and a completion status.

**2. Expose agent registry reload as a public API**

Refreshing the agent registry (re-reading and re-registering all agent definitions) is currently only possible through a private internal method on the configuration object. Tests and other code that needs to programmatically trigger a reload are forced to use TypeScript workarounds to access a private method. The agent registry should expose a public reload method directly, making the configuration object's private method unnecessary for external callers.

## Expected Behavior

- The reassembler for agent results should allow callers to retrieve accumulated in-progress messages as a list of activity items
- Each activity item should include a sequential identifier, a type indicating it represents agent reasoning, the message text, and a completed status
- The agent registry should have a public method to reload/refresh all registered agents without requiring access to internals of the configuration object

## Why This Matters

This enables UI components and other consumers to display the agent's intermediate reasoning steps, and removes the need for unsafe private method access when reloading agent configurations.
