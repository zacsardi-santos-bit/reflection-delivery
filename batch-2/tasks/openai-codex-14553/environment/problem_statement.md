## Description

The functions that build permissions instructions for AI agents need to be extended to support a new feature flag indicating whether a session-level permissions-requesting tool is available. Currently, the instruction-generation functions have no awareness of this capability, so sessions that enable it cannot communicate its availability to the agent.

## Expected Behavior

- A new boolean parameter should be added to the functions that construct permissions instructions, indicating whether the permissions-requesting tool is enabled for the current session.
- When the feature is disabled (the parameter is off), the generated instructions must be identical to what they were before the parameter was added — no regressions in existing output.
- All places in the codebase that call these instruction-building functions must be updated to pass the appropriate value for the new flag, so that the project continues to compile and existing tests continue to pass.

## Why This Matters

Without this change, adding the new parameter only to part of the call chain causes the project to fail to compile. Updating all callers ensures backward compatibility and unblocks sessions that need to communicate the tool's availability to agents.
