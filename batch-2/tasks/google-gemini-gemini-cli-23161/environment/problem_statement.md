## Description

When a user enables or disables a subagent through configuration settings while a session is running, the change should take effect dynamically. Currently, the tool registry is only populated during initialization and does not update when agent settings change at runtime. This means that toggling an agent's enabled/disabled state has no effect until the application is fully restarted.

## Expected Behavior

- When an agent is disabled after previously being active, it should be removed from the available tools the next time the agent configuration is refreshed.
- When an agent is enabled after previously being inactive, it should be added to the available tools the next time the agent configuration is refreshed.
- Agents that are disabled from the very start should never appear in the tool registry.
- Agents should always be available as tools even if they are not explicitly listed in the general tools allow-list — subagent availability should be governed by their own enabled/disabled setting.

## Why This Matters

Users who configure their agents interactively expect changes to take effect without a restart. Without this capability, the system is inconsistent: the displayed configuration shows a setting as disabled, but the tool remains available (or vice versa). Proper runtime synchronization between agent settings and the tool registry makes the experience predictable and correct.
