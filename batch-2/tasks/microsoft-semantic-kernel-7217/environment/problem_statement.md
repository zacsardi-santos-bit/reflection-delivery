## Description

The Azure AI Inference connector in semantic-kernel is missing support for automatic function/tool calling, has bugs in message formatting utilities, and has an inconsistent method signature for embedding generation.

## Problems

**Function/Tool Calling Not Supported**
When a model responds with a tool call request, the connector has no mechanism to validate prerequisites (such as requiring a kernel to be present), enforce constraints (such as only supporting a single completion when auto-invoking tools), or automatically invoke kernel functions and continue the conversation loop. Both regular and streaming chat completion paths are affected.

**Message Formatting Bugs**
The assistant message formatter incorrectly builds the message content as a list of content items instead of passing the message text directly as a string. The tool message formatter silently logs a warning when the message is malformed (first item is not a function result) instead of raising an error to alert the caller.

**Embedding Method Signature Inconsistency**
The embedding generation method requires the execution settings to be passed as a named keyword argument, but the rest of the codebase passes settings as a positional argument. This inconsistency causes calls using positional argument style to fail to forward settings correctly.

## Expected Behavior

- Attempting to use automatic function invocation without providing a kernel should result in an error.
- Attempting to use automatic function invocation while requesting more than one completion should result in an error.
- When a tool call is returned and auto-invocation is configured, the service should loop through invocations up to the configured limit and report the final result with an appropriate finish reason.
- Assistant messages with tool calls should format the content as a string, with tool call details in a separate field.
- A malformed tool message (where the first item is not a function result) should raise an error rather than silently continuing.
- Embedding generation should accept execution settings as a regular positional argument, and those settings should not leak into the underlying API call's extra keyword arguments.

## Why This Matters

Without these fixes, developers using the Azure AI Inference connector cannot build agentic workflows that rely on automatic function calling, and may encounter silent data corruption or unhelpful error messages when working with message history or tool results.
