## Description

WebSocket error handlers unintentionally share the CDI request context from the message handler that triggered the error.

When a binary message handler encounters a runtime exception and the framework delegates to a registered error handler, the error handler is currently executed within the same CDI request context that was active during the original message handler. This means any request-scoped beans the error handler accesses will already contain state that was set during the failed message handler — which is unexpected and incorrect behavior.

## Expected Behavior

- When an error handler is invoked to handle a runtime exception thrown by a WebSocket binary message handler, the error handler should run within a **fresh** CDI request context.
- Request-scoped beans accessed in the error handler should be in their default (newly initialized) state, not contaminated by state from the triggering handler's context.
- This clean-context guarantee should hold regardless of the execution thread model — both event loop threads and worker threads must each get a fresh context when error handling begins.

## Why This Matters

Error handlers are conceptually independent of the handlers that trigger them. Sharing request context between a failed message handler and the error handler creates surprising coupling: the error handler sees side effects from the very operation it is supposed to handle, which makes it difficult to implement reliable error recovery logic.
