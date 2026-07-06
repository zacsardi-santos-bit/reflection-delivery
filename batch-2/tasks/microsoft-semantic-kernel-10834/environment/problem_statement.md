## Description

The OpenAI Realtime service currently exposes all of its internal helper utilities and event type definitions from its main public module. These internal utilities should not be part of the public API and should be moved into a private internal module. Additionally, the attribute used to hold the orchestration object within realtime service instances is currently public, which allows unintended external access and modification.

## Expected Behavior

- Internal helper utilities and event type definitions for the realtime service should live in a separate private module (indicated by an underscore prefix in the module name), not in the main public module.
- The main public module should only export the public service classes used by consumers.
- The attribute on the realtime service instances that holds a reference to the orchestration object should be made private (indicated by an underscore prefix), encapsulating internal state properly.

## Why This Matters

Exposing implementation details as part of the public API makes it harder to maintain and evolve the codebase. Moving internal utilities to a private module and making internal state attributes private ensures that consumers depend only on the intended public interface, and that internal changes do not unintentionally break external code.
