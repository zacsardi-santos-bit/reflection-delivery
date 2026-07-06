## Update AI SDK Adapter to Match New SDK Event Shapes

The third-party AI SDK has released a new version that changed the shapes and names of streaming event objects as well as the format for defining tool schemas. Our adapter code that wraps this AI SDK has not been updated to reflect these changes, causing it to break when used with the new SDK version.

## Specific Issues

- **Streaming text events**: The field carrying text content has been renamed. The adapter still reads the old field name.
- **Reasoning events**: The event type name has changed, and the content field has also been renamed.
- **Tool call events**: The field carrying tool call arguments has been renamed.
- **Streaming tool input delta events**: The event type name and all its fields have been renamed.
- **Finish events**: The token usage object has been moved to a differently named property, and the individual token count field names have also changed.
- **Step lifecycle events**: Both the "step start" and "step end" event type names have been renamed, and their shapes simplified.
- **File parts**: The file metadata is now nested inside a sub-object rather than being at the top level.
- **Tool input start events**: The event type and its identifier field have been renamed.
- **Tool schema format**: When passing tool definitions to the AI SDK, the parameter schema must now be provided under a different property name.

## Expected Behavior

After updating, the adapter should correctly parse all new event shapes from the AI SDK, convert them to the equivalent OpenAI-compatible streaming chunks, and handle tool definitions with the new schema property name.

## Why This Matters

Any user relying on the AI SDK-backed providers for streaming completions or tool use will experience broken behavior until this adapter is updated to match the new SDK contract.
