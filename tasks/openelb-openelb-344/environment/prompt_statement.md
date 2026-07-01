I'm working on the IPAM controller for an external load balancer operator in Kubernetes, and I need to implement the core IP allocation management logic with proper unit test coverage.

Specifically, I need three methods on the IPAM manager type:

The first method should look at a given Kubernetes service and the current state of the EIP pools to decide what action is required — allocate a new IP, release an existing one, both (when switching pools), or nothing at all. It needs to handle cases like: the service is nil, the service is not a load balancer type, the service lacks the required annotations, the service is being deleted, the service has lost its load balancer annotation while still holding an IP, and the service is moving to a different IP pool.

The second method should take an allocation record and actually assign an IP from the named EIP pool to the service. It should return an error in all unavailability scenarios: the pool is being deleted, the pool is administratively disabled, the pool uses a different protocol than requested, the requested static IP falls outside the pool's range, or the pool has no remaining capacity. On success it should update the service with a finalizer, a label pointing to the EIP, and the allocated IP in the load balancer ingress status.

The third method should take a release record and free the IP back to the pool, cleaning up the service's finalizer, label, and ingress status. If the EIP no longer exists, it should still clean up the service's state without returning an error.

All three methods should be usable with a fake Kubernetes client so they can be tested without a live cluster.
