## Description

When the node agent (kubelet) on a Kubernetes node restarts, it removes all device plugin socket files and expects device plugins to detect this and re-register themselves. Currently, KubeVirt's device plugins have no ability to detect socket file removal events. Once the socket is deleted, the device manager remains in a broken state and virtual machines can no longer be scheduled or started on that node — even though the underlying hardware devices are still physically present.

Additionally, there is no retry logic for device plugins that fail to start: a device plugin that encounters a transient error has no mechanism to recover automatically with appropriate backoff delays.

## Expected Behavior

- When the device plugin socket file is deleted (indicating the node agent has restarted), the device manager should detect this event and automatically perform a clean re-registration with the node agent.
- When a device plugin exits cleanly as part of a re-registration cycle, it should be restarted immediately without any delay.
- When a device plugin fails to start due to an error, it should be restarted with exponential backoff delays to avoid hammering a system that may need time to recover.
- Multiple device plugins should be managed concurrently so that one unavailable device does not prevent other device plugins from starting.
- After a node agent restart and re-registration cycle, it should still be possible to start new virtual machines on the same node.

## Why This Matters

Without automatic re-registration after a kubelet restart, an operator would need to manually intervene to restore device plugin functionality on the affected node. This creates a reliability gap where routine node-level operations (like kubelet upgrades or restarts) can render a node unable to run virtual machines until a human fixes it.
