Implement support for declaring additional named ports in the container and service traits of a Kubernetes integration framework. Ensure that both traits accept a list of port definitions in a semicolon-separated format, allowing for custom port configurations.

*   Update the `ContainerTrait` struct in `pkg/apis/camel/v1/trait/container.go`:
    *   Add a new field: `Ports []string` with struct tags `property:"ports" json:"ports,omitempty"`.
    *   Parse each entry in `Ports` during the Configure phase to produce `corev1.ContainerPort` objects.
    *   Ensure entries are in the format "port-name;port-number[;port-protocol]".
        *   Default protocol to TCP if omitted.
    *   Apply container ports only in non-Knative environments.
    *   Return errors for improperly formatted entries:
        *   If fewer than 2 parts: "container trait configuration failed: could not parse container port <entry> properly: expected format \"port-name;port-number[;port-protocol]\"".
        *   If port number is not an integer: "container trait configuration failed: could not parse container port number in <entry> properly: expected port-number as a number".

*   Update the `ServiceTrait` struct in `pkg/apis/camel/v1/trait/service.go`:
    *   Add a new field: `Ports []string` with struct tags `property:"ports" json:"ports,omitempty"`.
    *   Parse each entry in `Ports` during the Configure phase to produce `corev1.ServicePort` objects.
    *   Ensure entries are in the format "port-name;service-port-number;container-port-number[;port-protocol]".
        *   Default protocol to TCP if omitted.
    *   Append custom service ports to any default ports in the service spec.
    *   Activate the service trait when custom ports are specified, even if HTTP exposure is not enabled.

*   Update `zz_generated.deepcopy.go` in `pkg/apis/camel/v1/trait/`:
    *   Ensure `ContainerTrait.DeepCopyInto` and `ServiceTrait.DeepCopyInto` properly deep-copy the new `Ports []string` field.
    *   Follow the standard pattern: check for nil, allocate, and copy.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.