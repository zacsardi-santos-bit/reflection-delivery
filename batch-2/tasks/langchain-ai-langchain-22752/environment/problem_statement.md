## Description

Working with LLM chat applications often requires managing conversation history before sending it to a model. Common operations include trimming history to fit a token limit, filtering messages by type or speaker, and combining consecutive same-role messages into one. Currently, the messages module doesn't provide any built-in utilities for these tasks, so developers are left writing the same boilerplate in every project.

## Expected Behavior

Three utility functions should be added to the messages module:

- A **merge** function that combines consecutive messages of the same type into a single message, joining text content with newlines and concatenating content blocks and tool calls for complex messages. It should leave messages of types that cannot be merged (such as tool response messages) untouched.

- A **filter** function that selects messages from a list based on their type, name, or ID. It should support both inclusion and exclusion criteria, and type filters should accept type names as strings, message class objects, or lists of either.

- A **trim** function that reduces a message list to fit within a token budget provided by a custom counting function. It should support keeping messages from the start or end of the list, optionally including partial messages (split at content-block or text boundaries), always preserving a leading system message when requested, and constraining which message type the result starts or ends on.

## Why This Matters

All three utilities should work both as plain functions (passing the message list directly) and as composable pipeline components (returned when called without a message list, accepting input via invocation). This makes them easy to drop into chains and agents without changing the calling convention.

These functions should be exported from the top-level messages namespace so they can be imported alongside existing message classes.
