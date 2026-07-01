Implement support for managing EndpointSlices in the kubelet controller alongside existing Endpoints. Ensure the controller can be configured to manage either or both resources, and handle node addresses appropriately based on IP address family and maximum endpoints per slice.

*   Update `CreateOrUpdateService` in `pkg/k8sutil/k8sutil.go` to return a `*v1.Service` and an `error`.
*   Define a `nodeAddress` struct in `pkg/kubelet/controller.go` with fields: `ipAddress`, `name`, `uid`, `apiVersion`, `ipv4`, and `ready`.
*   Modify `getNodeAddresses` method in `pkg/kubelet/controller.go` to accept `[]v1.Node` and return `([]nodeAddress, []error)`.
*   Ensure the `Controller` struct in `pkg/kubelet/controller.go` includes unexported fields: `logger`, `kclient`, `kubeletObjectName`, `kubeletObjectNamespace`, `manageEndpoints`, `manageEndpointSlice`, and `maxEndpointsPerSlice`.
*   Define `ControllerOption` type and option constructors: 
    *   `WithEndpoints()`
    *   `WithEndpointSlice()`
    *   `WithMaxEndpointsPerSlice(v int)`
    *   `WithNodeAddressPriority(s string)`
*   Update `New` function in `pkg/kubelet/controller.go` to accept separate `kubeletServiceName` and `kubeletServiceNamespace` strings, and variadic `ControllerOption`. Return an error if neither `WithEndpoints` nor `WithEndpointSlice` is provided.
*   Implement `sync` method in `pkg/kubelet/controller.go` to:
    *   List nodes and convert them to `nodeAddress`.
    *   Create/update the Service and conditionally manage Endpoints and/or EndpointSlices.
    *   Create separate EndpointSlices for IPv4 and IPv6 when `WithEndpointSlice` is enabled.
    *   Respect `maxEndpointsPerSlice` limit, creating new slices as needed.
    *   Delete EndpointSlices that become empty.
    *   Update existing EndpointSlices in place to preserve identity.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.