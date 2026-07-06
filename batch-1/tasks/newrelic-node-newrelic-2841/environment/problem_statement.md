## Description

We need to add health reporting support to the Node.js agent so it can integrate with centralized agent management systems in containerized environments. When enabled via configuration, the agent should periodically write a structured status file to a designated directory, giving external management tooling a way to detect whether the agent is functioning correctly without needing to parse logs or access internal metrics.

## Expected Behavior

- A new configuration block should control whether health reporting is enabled, where status files are written (as a directory path or file URI), and how often the agent writes updates.
- When enabled, the agent must write a status file at regular intervals containing: a boolean health indicator, a human-readable status message, an error code, and timestamps for when the agent started and when the status was last updated.
- The health status must automatically reflect what is happening with the agent:
  - If the agent is disabled via configuration, the status file should show an unhealthy state with an appropriate error code.
  - If the agent fails to connect or encounters backend errors (including invalid license key, missing license key, proxy misconfiguration, forced disconnect, or general connection failure), the status should reflect the specific problem.
  - If the application name is not configured or an unexpected internal error occurs during startup, the status should be updated accordingly.
  - When the agent shuts down cleanly (and was previously healthy), the status file should reflect a clean shutdown. If the agent shuts down in an unhealthy state, the last error code should be preserved.
- Health reporting configuration should be settable via environment variables in addition to the configuration file.
- When health reporting is disabled (via configuration) or when the output directory is inaccessible, the agent should log an appropriate message and skip all health reporting activity.

## Why This Matters

Infrastructure teams running agents in Kubernetes or similar container orchestration environments need a reliable, standardized way to monitor agent health through the management control plane. Without this capability, operators cannot tell whether agents are functioning correctly without manually inspecting agent logs. This change enables automated health monitoring at the fleet level.
