## Description

The admin API client manager in the Kubernetes Ingress Controller has two related problems that need to be addressed:

1. **Incorrect endpoint exclusion logic**: The endpoint discovery currently excludes endpoints based on whether they are marked as "not ready." This is too aggressive — an endpoint that is temporarily not-ready but is not being terminated should still be included as a candidate. Only endpoints that are actively terminating should be excluded. This means that non-ready, non-terminating endpoints are incorrectly dropped, and ready-but-terminating endpoints may incorrectly be included.

2. **No support for clients that need time to become ready**: The client manager currently has no mechanism for tracking admin API clients that have been discovered but cannot yet accept connections. If a client fails to connect at creation time, it is simply discarded. There is no periodic retry, and no way to promote a previously-failing ("pending") client to active ("ready") status once it becomes reachable. Similarly, there is no way to demote an active client to pending if it becomes temporarily unreachable.

## Expected Behavior

- Endpoint discovery should include endpoints that are not-ready but not-terminating, and exclude endpoints that are terminating (regardless of ready status).
- The client manager should maintain two sets of clients: those that are currently reachable ("ready") and those that have been discovered but are not yet reachable ("pending").
- A dedicated readiness-checking component should periodically examine both sets, promoting pending clients that have become reachable and demoting ready clients that have become unreachable.
- The manager should start its processing loop via an explicit method call, and should support a configurable interval for periodic readiness reconciliation.
- Creating an admin API client manager without any initial clients should produce a descriptive error message.

## Why This Matters

Without this fix, the controller may fail to connect to healthy admin API endpoints that are temporarily not-ready, and it has no way to recover connections to endpoints that were unreachable at startup but later become available. This can cause unnecessary disruption in dynamic environments where admin API pods restart or roll out gradually.
