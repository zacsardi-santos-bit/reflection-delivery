## Description

The agent framework currently has no built-in concept of operational phases or modes. When building agents that need to behave differently depending on the stage of work — for example, collecting requirements and seeking approval versus autonomously executing tasks — developers have no structured way to configure, store, or transition between these phases. Everything must be built from scratch each time.

## Expected Behavior

- Developers should be able to configure an agent with named operational modes, each with its own behavioral description.
- The agent's current mode should be tracked in session state and persist across interactions within a session.
- Helper functions should allow reading and writing the current mode directly from session state, with input normalization (whitespace trimming, case folding) and validation.
- If an unrecognized mode is set, the system should raise a clear error.
- If existing session state for the mode key is not in the expected format, the system should raise a descriptive error rather than silently overwriting it.
- If the previously persisted mode is no longer available in the configured set, the system should gracefully fall back to the default mode.
- The agent itself should be able to read and update its mode through dedicated tools injected at runtime.
- Instructions provided to the agent should clearly describe each available mode and indicate which mode is currently active.
- All mode-related APIs should be marked as experimental (part of the harness feature group).

## Why This Matters

Structured mode-aware workflows — such as a "plan then execute" pattern — are a common pattern for building safer, more controllable AI agents. Having this as a first-class primitive in the framework reduces boilerplate and makes agent behavior more predictable and auditable.
