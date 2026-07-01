## Description

Beyla's context propagation feature — which injects distributed trace headers into outgoing HTTP requests using Linux Traffic Control — is currently disabled by default. This means users who want distributed tracing between services must explicitly enable it in their configuration, even though it is a generally useful capability that should work out of the box.

## Expected Behavior

- Context propagation should be enabled by default when Beyla starts with no explicit configuration for this setting.
- Users who do not want context propagation can still disable it through configuration, but enabling it should not be required for the common case.

## Why This Matters

Distributed tracing is a core observability feature. Requiring users to discover and manually enable this setting creates unnecessary friction and means many users miss out on the capability entirely. Making it the default ensures better out-of-the-box observability for all Beyla deployments.
