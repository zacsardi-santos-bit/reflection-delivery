## Description

Pods created during operator bundle unpacking are launched without proper security hardening. Unlike other pods in the system, these temporary unpacking pods don't have a pod-level security context set — they don't specify a non-root user, don't apply a seccomp profile, and their containers don't explicitly disallow privilege escalation or drop Linux capabilities. This is a security gap that should be addressed to meet production security requirements.

Additionally, catalog source registry pods are missing an explicit declaration that they should not run in privileged mode, even though other security fields are already set on those containers.

## Expected Behavior

- All pods created for bundle unpacking should run as a specific non-root user (matching the user defined in the operator registry image)
- Bundle unpacking pod containers (both main and init containers) should explicitly forbid privileged execution, disallow privilege escalation, drop all Linux capabilities, and use the runtime-default seccomp profile at the pod level
- Catalog source registry pods should explicitly declare that containers must not run in privileged mode

## Why This Matters

Running unpacking pods without explicit security constraints makes it harder to enforce pod security admission policies and leaves the door open for unintended privilege escalation. Applying consistent, minimal security settings across all operator lifecycle pods reduces the attack surface and ensures the system can operate in environments with strict security requirements.
