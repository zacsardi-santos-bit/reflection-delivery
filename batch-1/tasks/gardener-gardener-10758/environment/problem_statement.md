## Description

The blackbox exporter scrape configuration builder for the garden cluster constructs static monitoring target configurations with labels. These labels are currently keyed using a specialized named type, but the monitoring API now expects the labels map to use plain string keys. This type mismatch causes compilation failures and prevents the scrape configs from being correctly constructed.

## Expected Behavior

- The labels attached to each static monitoring target configuration (for the API server, Kubernetes API server, dashboard, and discovery server) should use a plain string map type for label keys.
- The availability purpose label on each static target config should be expressed with a plain string key, not a specialized named type.

## Why This Matters

The upstream monitoring library has updated its API so that the labels field on static scrape configurations now uses plain string keys rather than a named string type. The gardener blackbox exporter scrape config builder needs to be updated to match this new type requirement, otherwise the code will not compile and monitoring configurations cannot be generated.
