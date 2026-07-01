Implement a method to wait for a Kubernetes service to become available by checking if it has been assigned an IP address. Ensure this method can handle timeouts and invalid duration inputs appropriately.

*   Implement the `WaitForService` method in `pkg/executables/kubectl.go` with the following signature:
    *   `WaitForService(ctx context.Context, kubeconfig string, timeout string, name string, namespace string) error`
*   Ensure the method:
    *   Accepts a context, a kubeconfig file path, a timeout string, a service name, and a namespace string as parameters.
    *   Returns an error if the operation fails or times out.
*   Invoke the `kubectl` executable with the following arguments in this exact order:
    *   "get", "--ignore-not-found", "--namespace", `<namespace>`, "service", `<name>`, "-o", "json", "--kubeconfig", `<kubeconfig>`
*   Return `nil` when:
    *   The `kubectl` response contains a Service with a non-empty `ClusterIP` in its `Spec` field.
    *   The `kubectl` response contains a Service with at least one LoadBalancer ingress entry that has a non-empty IP address in its `Status`.
*   Return a non-nil error immediately if:
    *   The timeout parameter cannot be parsed as a valid Go duration string.
*   Return a non-nil error when:
    *   The service is not found (empty or missing response) and the specified timeout duration elapses.
    *   The error should indicate that the context was canceled or the deadline exceeded.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.