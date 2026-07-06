## Description

Several related gaps need to be addressed in the ztunnel integration:

1. **Missing mixed-enrollment traffic scenarios**: The connectivity test suite currently only covers traffic between two enrolled pods or two unenrolled pods. It is missing scenarios where one side is enrolled in the ambient mesh and the other is not — which is a realistic deployment case that should be validated. Plain (non-encrypted) traffic should be expected in these mixed cases.

2. **Pod identity not preserved in endpoint slices**: When a pod's endpoint information is stored in a compact representation, the pod's unique identifier (UID) from its owner reference is not preserved. This means consumers of that compact representation cannot reconstruct the Kubernetes owner reference to link back to the originating pod. The conversion functions that translate between the full endpoint representation and the compact form should round-trip the pod UID through the owner reference mechanism.

3. **xDS endpoint subscription does not respect namespace enrollment**: The xDS server's endpoint subscription mechanism currently sends all known endpoints as an initial list when a consumer subscribes. It does not filter for namespaces enrolled in the ambient mesh, and it relies on a "list all" approach instead of event batching. The subscription model should be changed so that pre-existing endpoint events are batched and delivered as an initial snapshot to the subscriber, while subsequent events are streamed individually. Endpoints in namespaces that are not enrolled must be silently ignored.

4. **Namespace enrollment does not notify the xDS layer**: When a namespace is enrolled in or removed from the ambient mesh, the system currently enrolls/disenrolls the local endpoints via the ztunnel daemon, but it does not notify the xDS server about the corresponding endpoint objects. The enrollment reconciler should emit endpoint creation events when a namespace is enrolled, and endpoint removal events when a namespace is disenrolled, using the endpoint resources from the Kubernetes store.

## Expected Behavior

- Connectivity tests cover all four mixed-enrollment traffic patterns: enrolled-to-unenrolled and unenrolled-to-enrolled, for both same-node and cross-node cases. These scenarios expect no encryption.
- The compact endpoint representation preserves the pod UID so that it can be reconstructed as a Kubernetes owner reference when converting back to the full representation.
- The xDS subscription mechanism buffers pre-sync events into an initial batch, forwards post-sync events individually, and filters out events for unenrolled namespaces.
- The enrollment reconciler emits endpoint events to the xDS channel whenever a namespace is enrolled or disenrolled.

## Why This Matters

Without mixed-enrollment tests, asymmetric connectivity issues could go undetected. Without pod UID preservation, downstream consumers cannot correlate endpoints with their pods. Without proper namespace filtering and event batching in the xDS subscription, ztunnel may receive information about endpoints it should not know about, or miss the initial state entirely.
