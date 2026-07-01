## Description

The postgres-operator currently has no way to detect the actual PostgreSQL version running inside a cluster's containers. When the operator syncs a StatefulSet, it only knows the desired version specified in the cluster spec — it cannot inspect what version is actually deployed and running. This creates a risk of generating incorrect configurations or silently ignoring upgrade requests that cannot be safely applied.

## Expected Behavior

- The operator should be able to read a container's runtime configuration to determine which PostgreSQL version is currently active. This version is embedded in the container's internal configuration as a binary directory path.
- Given a binary directory path and a path format template, the operator should be able to extract the PostgreSQL version string (e.g., "9.6", "11", "12") from that path, supporting both decimal and integer version formats.
- When syncing a cluster's StatefulSet, the operator should compare the currently running version against the desired version. If they differ, the running version should take precedence and be preserved — since in-place major version upgrades are not supported.

## Why This Matters

Without this capability, the operator cannot distinguish between a cluster running version 11 and one running version 12 — it only knows what version was requested. This can lead to silently inconsistent state during StatefulSet synchronization. Detecting the actual running version and preserving it prevents unintended configuration drift and incorrect StatefulSet generation.
