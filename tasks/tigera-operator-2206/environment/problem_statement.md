## Description

The Dex authentication service container is deployed without some important security hardening settings. Specifically, the container's security configuration does not drop all Linux capabilities, and it does not apply the runtime default isolation profile at the process level. This means the container retains more system-level privileges than it actually needs, which creates an unnecessary attack surface and prevents deployment in environments that enforce a baseline pod security standard.

## Expected Behavior

- The Dex container should be configured to drop all Linux capabilities as part of its security context.
- The Dex container should apply the runtime default process isolation profile (seccomp) in its security context.
- The existing security settings (non-root user/group enforcement) should remain in place alongside these new settings.

## Why This Matters

Many Kubernetes environments enforce a baseline pod security standard that requires containers to drop capabilities and use an appropriate seccomp profile. Without these settings, the Dex pod may be rejected by the API server in hardened clusters, and in permissive clusters it runs with unnecessarily broad privileges.
