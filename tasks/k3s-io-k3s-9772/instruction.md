Implement a centralized utility package in the k3s project to map cluster service names to their associated certificate and key file paths. Create a function that accepts a control configuration and a list of service names, returning a map of service names to file paths. Ensure the function returns an error for unrecognized service names and provide pre-defined service groupings.

Requirements:

*   Implement the `FilesForServices` function in `pkg/util/services/services.go` with the signature:
    ```go
    FilesForServices(controlConfig config.Control, services []string) (map[string][]string, error)
    ```
    *   Accept a `config.Control` struct with `DataDir` and `Runtime *config.ControlRuntime` fields.
    *   Return a map where each key is a recognized service name and each value is an ordered list of certificate and key file paths.
    *   Return an error if any service name in the input slice is unrecognized.

*   Define the `All` variable in `pkg/util/services/services.go`:
    *   Type: `[]string`
    *   When passed to `FilesForServices` with `DataDir='/var/lib/rancher/k3s/server'`, return a map with keys: 'admin', 'api-server', 'auth-proxy', 'cloud-controller', 'controller-manager', 'etcd', 'k3s-controller', 'kube-proxy', 'kubelet', 'scheduler'.
    *   Ensure file paths match the specified lists for each service.

*   Define the `Server` variable in `pkg/util/services/services.go`:
    *   Type: `[]string`
    *   When passed to `FilesForServices` with `DataDir='/var/lib/rancher/k3s/server'`, return a map with keys: 'admin', 'api-server', 'auth-proxy', 'cloud-controller', 'controller-manager', 'etcd', 'scheduler'.
    *   Ensure file paths match the specified lists for each service.

*   Define the `Agent` variable in `pkg/util/services/services.go`:
    *   Type: `[]string`
    *   When passed to `FilesForServices` with `DataDir='/var/lib/rancher/k3s/server'`, return a map with keys: 'k3s-controller', 'kube-proxy', 'kubelet'.
    *   Ensure file paths match the specified lists for each service.

*   Define the `CertificateAuthority` constant in `pkg/util/services/services.go`:
    *   Type: `const string`
    *   Value: `"certificate-authority"`
    *   When passed to `FilesForServices` as `[]string{CertificateAuthority}`, return a map with key 'certificate-authority' containing ten CA cert/key file paths.

*   Derive file paths in `FilesForServices` from the `Runtime` fields of `config.Control`.
    *   For services with both server-side and agent-side files, locate agent-side files in the directory obtained by joining the parent of `DataDir` with 'agent' (e.g., `/var/lib/rancher/k3s/agent`).

*   Name the package `services` and ensure it is located at `pkg/util/services/services.go` in the k3s repository.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.