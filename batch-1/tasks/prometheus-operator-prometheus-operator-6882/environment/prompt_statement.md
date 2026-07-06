I'm working on the kubelet controller in prometheus-operator, and I need to add support for managing EndpointSlices alongside the existing Endpoints resource. Right now the controller only creates and updates legacy Endpoints objects for kubelet node discovery, but for large clusters this doesn't scale well. EndpointSlices are the modern replacement and we'd like to support both.

The changes I need are roughly:

The controller should be configurable (via functional options) to manage Endpoints, EndpointSlices, or both. When EndpointSlice management is enabled, IPv4 and IPv6 node addresses need to be placed into separate slices (since each EndpointSlice only supports a single address family). There should also be a configurable maximum number of endpoints per slice — once a slice hits the limit, new endpoints go into a new slice of the same address type. When nodes are removed and a slice becomes empty, it should be deleted. Across multiple sync cycles, existing slices should be updated in place rather than recreated from scratch.

Additionally, the function that creates or updates a Service needs to return the resulting Service object (not just an error), because the EndpointSlice logic needs the service's identity to set owner references on the slices it creates.

The controller constructor signature also needs to change: instead of receiving a combined "namespace/name" string for the kubelet service, it should accept the name and namespace as separate parameters. The node address priority, Endpoints management, EndpointSlice management, and max-endpoints-per-slice settings should all be passed as functional options rather than positional parameters.

I'd like any implementation to handle the case where neither Endpoints nor EndpointSlice management is enabled — that should be an error at construction time.
