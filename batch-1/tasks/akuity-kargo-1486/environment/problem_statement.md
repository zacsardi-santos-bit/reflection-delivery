## Description

When a new Project is created in Kargo, the system needs to perform several initialization steps: creating a dedicated namespace and granting the API server permission to manage secrets within that namespace. Currently, these steps are bundled together in a single operation, which creates two problems:

1. **Lack of failure distinction**: Transient failures (that could succeed on retry) and permanent failures (like a namespace conflict that truly cannot be resolved) are treated the same way. The project's status doesn't accurately reflect whether it's still initializing and waiting for a retry, or whether it has encountered an unrecoverable error.

2. **Missing permission setup**: The webhook that handles project creation does not grant the Kargo API server scoped access to manage secrets in the new project namespace. As a result, the API server either lacks access or must be given overly broad cluster-wide permissions.

## Expected Behavior

- Project initialization should be split into discrete steps: first ensuring the namespace exists with proper ownership, then establishing the necessary role binding that grants the API server permission to manage secrets in that namespace.
- Both the management controller reconciler and the webhook should participate in this two-phase setup.
- When namespace setup fails in a recoverable way, the project should remain in an "initializing" state (so it will be retried). When the failure is unrecoverable (e.g., a namespace conflict), the project should be marked as permanently failed.
- The "Ready" phase should only be set after all initialization steps have completed successfully.
- A role binding granting the Kargo API server access to manage secrets should be created in each project's namespace at initialization time. If the binding already exists, the operation should be treated as a success (idempotent).

## Why This Matters

This change enables correct failure reporting for Project initialization and removes the need for the Kargo API server to have cluster-wide access to secrets. Instead, it receives per-namespace access at the time each Project is created, following the principle of least privilege.
