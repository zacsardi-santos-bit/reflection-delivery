Implement the core IP allocation management logic for the IPAM controller in a Kubernetes environment. Develop unit tests to ensure comprehensive coverage of the IP allocation and release operations for LoadBalancer services.

*   Implement the `NewManager` function:
    *   Accept a `controller-runtime` client.
    *   Return a `*Manager` instance exposing the `Get` method and the `ConstructRequest`, `AssignIP`, and `ReleaseIP` methods.

*   Implement the `ConstructRequest` method:
    *   Return `(nil, nil)` if the service argument is `nil`.
    *   Return a `Request` with a `nil Allocate` field for a `ClusterIP` service without an existing LoadBalancer ingress IP, even with OpenELB annotations.
    *   Return a `Request` with a `nil Allocate` and a populated `Release` record for a `ClusterIP` service with an existing LoadBalancer ingress IP.
    *   Return `(nil, nil)` for a `LoadBalancer` service lacking complete OpenELB annotations.
    *   Return a `Request` with an `Allocate` record for a `LoadBalancer` service with full OpenELB annotations and no existing EIP record.
    *   Use the annotation-based IP as the requested IP in the `Allocate` record, favoring it over `spec.LoadBalancerIP`.
    *   Populate the `Allocate` record's IP field from `spec.LoadBalancerIP` if no annotation-based IP is set.
    *   Return a `nil Allocate` record if the requested IP is already allocated to another service, unless owned by the service itself.
    *   Return a `Release` record if the service has a deletion timestamp.
    *   Return a `Release` record if the service lacks the OpenELB enable annotation but still has an EIP record.
    *   Return both `Allocate` and `Release` records when switching EIPs.

*   Implement the `AssignIP` method:
    *   Return `nil` without modification if the `allocate` argument is `nil`.
    *   Return an error if the EIP resource does not exist, is being deleted, is disabled, has a protocol mismatch, or if the requested IP is out of range or the pool is full.
    *   On success, update the service with a finalizer, a label pointing to the EIP, and set the LoadBalancer ingress IP.
    *   Update the EIP's `Status.Used` map after a successful assignment.
    *   Succeed and populate the service if the requested IP is already in the EIP's `Used` map.

*   Implement the `ReleaseIP` method:
    *   Return `nil` without modification if the `release` argument is `nil`.
    *   Return `nil` if the EIP resource does not exist, but clear existing OpenELB labels, finalizers, or ingress status from the service.
    *   Remove the service's entry from the EIP's `Status.Used` map and clear the service's finalizers, labels, and LoadBalancer ingress status when the EIP exists.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.