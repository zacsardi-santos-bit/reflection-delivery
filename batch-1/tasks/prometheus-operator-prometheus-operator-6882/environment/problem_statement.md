## Add EndpointSlice Support to the Kubelet Controller

## Description

The kubelet controller currently only manages the legacy Endpoints resource to track Kubernetes node addresses for kubelet service discovery. However, Endpoints have known scalability limitations — large clusters can produce very large Endpoints objects. The modern Kubernetes replacement is EndpointSlices, which splits endpoint data into smaller, more manageable pieces and is the preferred API going forward.

We need the kubelet controller to optionally manage EndpointSlices in addition to (or instead of) traditional Endpoints. When EndpointSlice management is enabled, the controller should:

- Automatically separate IPv4 and IPv6 node addresses into distinct EndpointSlices (since EndpointSlices have a single address type)
- Respect a configurable maximum number of endpoints per slice, creating additional slices when the limit is reached
- Delete EndpointSlices that become empty when nodes are removed
- Preserve existing EndpointSlice identities across sync cycles so that slices are updated in place rather than recreated

## Expected Behavior

- The controller should be configurable to manage Endpoints, EndpointSlices, or both simultaneously
- EndpointSlices must be grouped by IP address family (IPv4 vs IPv6)
- The maximum number of endpoints in a single slice must be configurable
- When all nodes in an EndpointSlice are removed, the slice must be deleted
- The function used to create or update a Service object should return the resulting Service resource so that it can be used by downstream operations (like setting owner references on EndpointSlices)

## Why This Matters

As Kubernetes clusters grow, the legacy Endpoints resource becomes a bottleneck. EndpointSlices are more efficient and are already the default in newer Kubernetes distributions. Supporting EndpointSlices allows operators running large clusters to take advantage of the improved scalability, while the existing Endpoints-based behavior is preserved for backward compatibility.
