## Description

The cluster configuration defaults file needs to be updated to reflect changes introduced by upgrading to a newer version of Kubernetes. Two new configuration parameters for controlling how the node agent handles image garbage collection should be added — one representing the upper disk usage threshold that triggers cleanup and another representing the lower threshold at which cleanup stops. These parameters currently have no defaults configured, which may lead to unexpected behavior.

Additionally, a configuration flag that was previously required to opt into time zone support for scheduled jobs should be removed. This feature is now part of the standard behavior in the newer version of Kubernetes and no longer needs to be explicitly toggled on. Keeping the flag in the configuration could cause confusion or unexpected behavior going forward.

## Expected Behavior

- The cluster defaults configuration includes both an upper and lower image garbage collection threshold for the node agent
- The scheduled job time zone feature flag is no longer present in the cluster defaults configuration

## Why This Matters

As Kubernetes evolves, some features that required explicit opt-in become standard, and new knobs are introduced to control existing behaviors. Keeping the cluster's default configuration in sync with the capabilities of the targeted Kubernetes version ensures predictable behavior and prevents stale configuration from accumulating.
