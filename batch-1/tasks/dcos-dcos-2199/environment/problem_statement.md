## Description

The Admin Router TLS configuration currently uses a single shared configuration file for both Master and Agent nodes. This is problematic because the two roles have different requirements: the Master Router is externally facing and needs to support a range of TLS protocol versions for browser and API client compatibility, while the Agent Router only serves cluster-internal traffic and should use a strict, modern-only TLS policy regardless of the external-facing configuration.

Additionally, operators can currently only toggle TLS 1.0 on or off, with TLS 1.1 and 1.2 always enabled together. There is no way to independently enable or disable each protocol version. This makes it impossible to support configurations like "TLS 1.2 only" or "TLS 1.0 and 1.2, but not 1.1".

## Expected Behavior

- The TLS configuration should be split into two separate files: one for Master Admin Router and one for Agent Admin Router.
- The Agent Admin Router configuration should always enforce TLS 1.2 only with an internal-traffic-optimized cipher suite, regardless of what TLS settings are configured for the master.
- Each TLS protocol version (1.0, 1.1, and 1.2) should be independently toggleable for the Master Admin Router.
- A safety validation should prevent any configuration where all three TLS versions are simultaneously disabled, since this would render the cluster inaccessible.
- The Master Admin Router should also support a configurable custom cipher suite override.

## Why This Matters

Different client populations (external browsers vs. internal cluster components) have different security needs. Giving operators fine-grained control over TLS protocol versions per router role improves both security posture and operational flexibility without breaking internal cluster communication.
