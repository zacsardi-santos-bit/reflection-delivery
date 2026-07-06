## Description

The Helm chart for Sloth renders empty security context sections in Kubernetes deployment manifests even when no security context values have been configured. When deploying with default values, the rendered deployment YAML contains empty security context keys at both the pod spec and container spec levels. This is incorrect behavior — security context sections should only appear when actual values are configured.

Additionally, the chart currently supports some security options that should be simplified away (supplemental group IDs and capability dropping), making the supported configuration surface larger than necessary.

## Expected Behavior

- When deploying with default values, no security context sections should appear anywhere in the deployment manifest (neither at the pod level nor at the container level).
- When security context values are explicitly configured, the configured fields should appear correctly in the rendered manifest at the appropriate level (pod or container).
- The supported pod-level security context settings should be: run-as user, run-as group, run-as non-root, and filesystem group.
- The supported container-level security context setting should be: privilege escalation prevention.
- Fields for supplemental groups and capability dropping should no longer be part of the supported security context configuration.

## Why This Matters

Deployers using the default Helm chart configuration get deployment manifests with unnecessary empty sections, which adds noise and can be confusing. Ensuring that security context blocks are only rendered when values are actually configured results in cleaner, more predictable Kubernetes manifests.
