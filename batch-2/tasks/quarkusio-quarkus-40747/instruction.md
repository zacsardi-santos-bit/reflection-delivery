Implement a mechanism to ensure that WebSocket error handlers operate within a fresh CDI request context when invoked due to a runtime exception in a binary message handler. Guarantee that request-scoped beans accessed by the error handler are in their default state, unaffected by the context of the failed message handler.

*   Ensure that when a WebSocket binary message handler throws a runtime exception, the subsequent error handler invocation occurs within a new CDI request context.
    *   The error handler must not share the request context that was active during the binary message handler.
*   Guarantee that request-scoped beans accessed in the error handler reflect their default, freshly initialized state.
    *   Prevent any state carryover from the triggering binary message handler's request context.
*   Apply the fresh-context behavior consistently across different thread models:
    *   Ensure that error handlers running on event loop threads receive a fresh request context.
    *   Ensure that error handlers running on worker threads also receive a fresh request context.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.