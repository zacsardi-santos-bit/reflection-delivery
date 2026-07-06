## Description

The Pulley virtual machine's constructor is currently infallible — it either succeeds or panics with no opportunity for the caller to recover. This is a problem because initializing the virtual machine involves allocating stack memory, and that allocation can fail (for example, when the requested stack size is extremely large or memory is constrained). The same issue affects the runtime store when users configure very large stack sizes: the system can panic instead of returning a meaningful error.

## Expected Behavior

- The virtual machine constructor should return a fallible result so callers can detect and handle initialization failures gracefully
- Similarly, the constructor for stacks with a configurable size should also be fallible
- A new fallible store creation function should be provided so that callers can handle the case where allocation fails (e.g., when the wasm stack or async stack size is configured to an extremely large value)
- When the stack size is set to the maximum representable value, the runtime should not panic — it should either fail gracefully or succeed

## Why This Matters

Users and embedders need to be able to configure extreme or unusual stack sizes without risking a panic. In constrained environments, OOM conditions during initialization should be recoverable errors, not hard crashes. This is also important for correctness when running under strict memory analysis tools.
