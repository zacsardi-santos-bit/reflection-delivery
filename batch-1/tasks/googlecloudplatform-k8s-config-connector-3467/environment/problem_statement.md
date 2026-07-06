## Description

The Spanner instance resource managed by Config Connector is missing support for two important configuration properties: the instance edition (which determines the feature tier and performance capabilities) and autoscaling configuration. Currently, users who manage Spanner instances through Config Connector cannot set these properties via their Kubernetes manifests, making it impossible to control the tier level or enable autoscaling through the operator.

Additionally, when autoscaling is enabled on a Spanner instance, the actual number of nodes and processing units allocated by the autoscaler are output-only fields that change over time. Currently, these values are not surfaced in the resource's observed state, so users have no way to see the current resource allocation from the Kubernetes object.

## Expected Behavior

- Users should be able to specify the instance edition in the resource spec when creating or updating a Spanner instance. Valid tiers include standard, enterprise, and enterprise plus.
- Users should be able to configure autoscaling settings (minimum and maximum node counts, and performance utilization targets) directly in the resource spec.
- When reconciling, if the edition has changed, it should be included in the update request to the cloud API.
- The actual current node count and processing units allocated (especially relevant when autoscaling is active) should appear as observed state fields in the resource's status after reconciliation.

## Why This Matters

Without edition support, Config Connector users cannot upgrade or downgrade their Spanner instance tier. Without autoscaling configuration support, users must rely on manual provisioning. Without observed state output for nodes and processing units, users lose visibility into what the autoscaler has actually allocated. Closing these gaps makes Config Connector a more complete tool for managing the full Spanner instance lifecycle.
