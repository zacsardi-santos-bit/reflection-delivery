## Description

When a reactive state change triggers a runtime error during rendering — such as attempting to render a component that doesn't exist — Svelte's effect tree becomes corrupted. This means that after the error, future state updates no longer work correctly: the application is effectively broken for the rest of its lifetime even though the error was limited to a single render cycle.

## Expected Behavior

- When a state flush causes a rendering error, the error should be surfaced to the caller so it can be handled
- After the error is handled, subsequent state updates should continue to work normally
- The DOM should correctly reflect any further reactive changes made after the error
- In synchronous mode, state changes that were part of the failed update should be preserved after recovery
- In async mode, state changes from the failed update should not be applied

## Current Behavior

After a rendering error during a synchronous flush, the internal reactive effect tree is left in a corrupted state. Subsequent state updates appear to have no effect on the DOM, and the application cannot recover.

## Why This Matters

This severely limits the ability to build resilient Svelte applications. If any single rendering error occurs — even in an isolated conditional block — the entire reactive system stops working, forcing a full page reload to restore functionality. Proper error recovery would allow applications to remain functional after catching and handling render-time errors.
