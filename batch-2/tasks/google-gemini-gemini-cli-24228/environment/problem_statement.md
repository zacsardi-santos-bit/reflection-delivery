## Description

When a user configures a limit on the number of browser actions the agent is allowed to take per task, internal automation operations—such as injecting or removing visual overlays and input-blocking scripts that the agent uses for its own housekeeping—are incorrectly counted toward that limit. This causes the action budget to be exhausted prematurely, preventing the agent from completing the actual work the user wanted it to do.

## Expected Behavior

- Internal infrastructure operations (e.g., managing overlays and input-blocking scripts) should NOT count against the user-configured action limit.
- Only meaningful browser interactions initiated on behalf of the user should be counted.
- When the action limit is reached based on actual user-facing actions, subsequent non-internal calls should be rejected with a clear error indicating the maximum action limit.

## Why This Matters

Users who configure a modest action budget to prevent runaway automation find their tasks terminated early not because of too many real browser interactions, but because the agent's own internal setup and teardown scripts consume the quota. This makes the action limit unreliable and frustrating to configure correctly.
