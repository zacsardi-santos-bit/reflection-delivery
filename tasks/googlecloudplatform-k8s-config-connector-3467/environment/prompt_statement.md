I'm working with Config Connector to manage Spanner instances through Kubernetes manifests, and I've run into a few gaps in the Spanner instance resource support.

First, there's no way to set the instance edition (the performance/feature tier) in the resource spec. I want to be able to specify whether an instance is standard, enterprise, or enterprise plus, and have that setting be applied when creating or updating the instance. If the edition changes in the manifest, it should be included in the update request sent to the cloud.

Second, I can't configure autoscaling through the resource spec at all. I'd like to set autoscaling limits (min/max nodes or processing units) and performance targets (CPU and storage utilization percentages) directly in the manifest.

Third, when autoscaling is active, the node count and processing units are output-only values controlled by the autoscaler. Right now these don't appear in the resource's status, so I have no way to see the current allocation from the Kubernetes object. I'd expect these to show up under the observed state in the status.

I also noticed that the CRD now exposes fields for maximum and minimum processing units as part of the autoscaling limits configuration, but there are no test fixtures that cover those fields. The missing-fields exceptions list should be updated to acknowledge those gaps.
