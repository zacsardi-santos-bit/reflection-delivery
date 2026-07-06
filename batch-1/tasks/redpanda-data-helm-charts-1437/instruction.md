Implement a Go function in the console chart package that constructs a typed Kubernetes Service object from chart values. Ensure the function reads the service type, port, and annotations from the chart values, derives the service name from the release name using the chart's existing naming convention, and returns a non-nil service object. Update the Helm template for the service to use this new Go function, enabling Go-level unit tests for service construction logic.

*   Implement the `Service` function in the `charts/console/service.go` file.
    *   Function signature: `Service(dot *helmette.Dot) *corev1.Service`.
    *   Ensure the function never returns nil when given valid service configuration.

*   Derive the service name:
    *   Use the existing `Fullname` helper function with the `dot` argument to set the `Name` field of the returned `Service` object.
    *   If `dot.Release.Name` is 'test', ensure the returned service's `Name` is 'test'.

*   Populate the Kubernetes Service object:
    *   Read service configuration (type, port, annotations) from `dot.Values` under the 'service' key.
    *   Use these values to populate the corresponding fields in the `Service` object.

*   Ensure the `Service` function is used by the Helm template layer to replace inline template logic with the Go function, making the service construction logic testable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.