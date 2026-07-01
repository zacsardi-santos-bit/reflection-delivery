Implement a mechanism to manage endpoint slices for external workloads in the Linkerd destination controller. Create functions to detect changes in workload configurations and determine which services require reconciliation upon updates.

*   Implement the `specChanged` function:
    *   Accept two `ExternalWorkload` resource pointers.
    *   Return `false` if both workloads have identical port configurations and IP addresses.
    *   Return `true` if there is a difference in the number of IP addresses or any individual IP address value.
    *   Return `true` if there is a difference in the number of ports or any port number.
    *   Return `true` if a port's name changes, including when the name is removed.

*   Develop the `EndpointsController`:
    *   Implement the `NewEndpointsController` constructor:
        *   Accept parameters: `*k8s.API`, `hostname` (string), `controllerNs` (string), and `stopCh` (chan struct{}).
        *   Return a `*EndpointsController` and an error.
    *   Ensure the `Start()` method can be called without errors or panics.
    *   Implement the `servicesToUpdate` method:
        *   Accept two `ExternalWorkload` resource pointers (old and updated).
        *   Return a slice of service keys in 'namespace/name' format and an error.
        *   Return an empty result if neither labels nor spec have changed.
        *   Return services currently selecting the workload if only the spec has changed.
        *   Return the symmetric difference of services if only labels have changed.
        *   Return the union of services matching old and new labels if both labels and spec have changed.

*   Update the k8s fake API infrastructure:
    *   Modify `NewFakeClusterScopedAPI` in `controller/k8s/test_helper.go` to include `ExtWorkload` in its resource type list.
    *   Update `NewFakeClientSets` in `pkg/k8s/fake.go` to handle `ExtWorkload` typed objects by appending them to the service-profile client object list.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.