## Description

The Rook Ceph object store operator currently has no way to configure which protocols the RADOS Gateway exposes. In deployments where S3 is not needed — for instance when using only Swift, or when Swift is configured at the root URL path — the operator should be able to selectively disable S3 and expose only the desired subset of gateway APIs.

Additionally, the readiness and liveness health probes on the gateway pod always assume S3 is available. When S3 is disabled, the probe target should fall back to the Swift health endpoint instead. If neither S3 nor Swift is active (e.g. only the admin API is enabled), the probe should be disabled entirely to avoid false failures.

## Expected Behavior

- Operators should be able to specify which gateway APIs to enable through the object store custom resource.
- Individual protocols (S3, Swift) should be disableable through protocol-specific options in the spec.
- Configuring Swift with a root URL prefix should automatically disable S3, since the two paths conflict.
- The explicit API list, when provided, should take precedence over per-protocol enable/disable settings.
- Gateway health checks should adapt to the active protocols: use the S3 path by default, fall back to the Swift path when S3 is off, or disable the probe when neither is available.
- Whitespace in API names should be normalized.

## Why This Matters

Without this capability, users who want a Swift-only object store are forced to work around the operator's assumptions about enabled APIs and health probe paths, leading to misconfigured deployments or broken health checks.
