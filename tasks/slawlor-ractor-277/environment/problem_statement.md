## Description

Working with supervisor actors in ractor requires managing children during shutdown, but there is currently no built-in API to stop or drain all of a supervisor's children at once. This makes it hard to write clean, correct shutdown logic in a supervisor's `post_stop` hook. Additionally, once you lose the strongly-typed reference to an actor (e.g. by storing it in a generic container), there is no way to check at runtime what message type that actor handles.

## Expected Behavior

- A supervisor actor should be able to stop all of its linked children at once, with or without waiting for them to fully exit.
- A supervisor actor should be able to drain all of its linked children at once, with or without waiting for them to fully exit.
- When a supervisor stops or drains all its children, those children's cleanup lifecycle hooks should be properly invoked.
- Stopping or draining all children of a supervisor (from outside the supervisor) should also cause the parent supervisor itself to shut down, since all child exits propagate stop notifications up the supervision tree.
- It should be possible to perform these child management operations from within the supervisor's own shutdown lifecycle hook safely.
- After losing the strongly-typed actor reference (going from a typed reference to a type-erased cell), it should be possible to check at runtime whether the actor handles a given message type. This check should return a definitive answer for local actors, and indicate that the check cannot be performed for remote actors.

## Why This Matters

Supervisor patterns require orderly, deterministic shutdown of supervised children. Without these APIs, developers have to manually iterate over children, track completion themselves, and can't guarantee that child cleanup hooks have run before the parent finishes shutting down. The runtime message type check is useful for generic containers and routing logic that need to verify actor compatibility without static type information.
