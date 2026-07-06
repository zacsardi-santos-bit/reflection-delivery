## Description

The router currently has no user-configurable setting for how long it will wait during startup to connect to a controller before giving up and exiting. The wait period is either absent or hardcoded, which gives operators no control over this behavior.

This is a problem in environments where network conditions vary significantly. In some deployments — such as routers that are geographically distant from controllers, or environments where the controller takes longer to become available — the current behavior is not flexible enough. In other environments, operators may want a quicker startup failure.

## Expected Behavior

- A new optional configuration setting should be available in the router's controller configuration section to specify a startup timeout duration.
- The router's internal configuration structure should expose this startup timeout as a settable duration field so that it can be tuned per deployment.

## Why This Matters

This gives operators explicit control over router startup behavior, making the system more predictable and easier to integrate into automated deployment and monitoring workflows.
