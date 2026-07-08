I'm hitting some gaps in the Spanner instance resource support in Config Connector and want to close them since I manage instances through Kubernetes manifests.

First problem, there's no way to set the instance edition (the performance/feature tier) in the resource spec. I want to specify whether an instance is standard, enterprise, or enterprise plus, and have that applied on create or update. Oh and if the edition changes in the manifest during reconcile, it needs to get included in the update request sent to the cloud API, right now it just doesn't.

Second, I can't configure autoscaling at all through the spec. I'd like to set autoscaling limits (min/max node counts or processing units) plus the performance targets (CPU and storage utilization percentages) directly in the manifest.

Third, when autoscaling's active the node count and processing units are output-only values the autoscaler controls, and they don't show up anywhere in the resource status today, so I can't see the current allocation from the Kubernetes object. I'd expect the actual allocated node count and processing units to surface as observed state fields under the status after reconciliation.

Also, actually, I noticed the CRD now exposes maximum and minimum processing units as part of the autoscaling limits config, but there aren't any test fixtures covering those fields yet, so the missing-fields exceptions list should get updated to acknowledge those gaps.

Without edition support I can't upgrade or downgrade the tier, without autoscaling config I'm stuck manually provisioning, and without the observed node/processing unit output I lose visibility into what the autoscaler actually did. Closing these makes it a more complete tool for the full Spanner instance lifecycle.
