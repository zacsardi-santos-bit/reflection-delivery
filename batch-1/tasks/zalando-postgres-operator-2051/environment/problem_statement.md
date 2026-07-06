## Description

When the postgres-operator schedules logical backup cronjobs, there is currently no way to inject environment variables from a pre-existing Kubernetes secret into those jobs. Users who need to supply sensitive configuration — such as cloud provider credentials or custom authentication tokens — to backup jobs are forced to either embed those values directly in the operator configuration or use workarounds. Neither approach is suitable for production environments with proper secret management practices.

## Expected Behavior

- The operator should accept a new configuration option that references a Kubernetes secret by name.
- When that option is set, all keys in the referenced secret should be injected as environment variables into the logical backup cronjob, with each variable sourcing its value from the secret rather than being embedded directly.
- When the option is not set, the behavior should remain unchanged — no additional environment variables are added.
- When the referenced secret does not exist, the operator should fail with a clear, descriptive error rather than silently misconfiguring the job.

## Why This Matters

Backup jobs often need access to cloud storage credentials or other sensitive values. Kubernetes secrets are the standard way to manage such values, and operators should support referencing them natively. Without this feature, users cannot follow least-privilege and secret-rotation best practices for their backup infrastructure.
