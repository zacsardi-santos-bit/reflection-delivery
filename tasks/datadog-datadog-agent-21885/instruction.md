Implement support for Google Kubernetes Engine (GKE) and Azure Kubernetes Service (AKS) in the Kubernetes compliance loader to correctly identify and extract kubelet security settings for these environments. Ensure the loader recognizes managed environments and processes kubelet configurations appropriately for each platform.

*   Detect managed environments:
    *   Set `K8sManagedEnvConfig.Name` to `"gke"` for GKE nodes and `"aks"` for AKS nodes.
    *   Identify GKE nodes by checking for a GKE-specific auth plugin in the kubelet's kubeconfig file.
    *   Identify AKS nodes by checking for a cluster server URL ending in '.azmk8s.io' or Azure-specific node labels.

*   GKE-specific requirements:
    *   Produce a result with no errors, non-nil `ManagedEnvironment`, nil control plane components, and a non-nil `Kubelet` with both a `Config` and a `Kubeconfig`.
    *   Extract kubelet configuration fields from the config file and ensure `AnonymousAuth`, `ReadOnlyPort`, `ClientCaFile`, and `AuthorizationMode` are nil in the `Kubelet` struct.
    *   Resolve `authentication.x509.clientCAFile` to a `*K8sCertFileMeta` object within the config file content.

*   AKS-specific requirements:
    *   Produce a result with no errors, non-nil `ManagedEnvironment`, nil control plane components, and a non-nil `Kubelet` with a `Kubeconfig` but a nil `Config`.
    *   Parse command-line flags to populate `Kubelet` struct fields: `AnonymousAuth` as false, `ReadOnlyPort` as 0, `EventQps` as 0, `MaxPods` as 110, `RotateCertificates` as true, and `AuthorizationMode` as 'Webhook'.
    *   Ensure `ClientCaFile`, `TlsCertFile`, and `TlsPrivateKeyFile` are non-nil `*K8sCertFileMeta` values with `User='root'`, `Group='root'`, `Mode=0644` for `ClientCaFile`, and `Mode=0600` for `TlsCertFile` and `TlsPrivateKeyFile`.
    *   Populate `TlsCipherSuites` with the cipher suite names from the `--tls-cipher-suites` flag.
    *   Set `FeatureGates` to a non-nil `*string` containing the raw feature gate string from the `--feature-gates` flag.

*   Update `K8sKubeconfigMeta` and `K8sKubeconfigUser` structs:
    *   Type `Kubeconfig` as `*K8SKubeconfig` to allow traversal of kubeconfig contents.
    *   Define `Exec` as a concrete struct with at least a `Command string` field for GKE detection.

*   Ensure the `Kubelet` configuration struct includes the following fields:
    *   `AnonymousAuth *bool`
    *   `ReadOnlyPort *int`
    *   `EventQps *int`
    *   `MaxPods *int`
    *   `RotateCertificates *bool`
    *   `AuthorizationMode *string`
    *   `ClientCaFile *K8sCertFileMeta`
    *   `TlsCertFile *K8sCertFileMeta`
    *   `TlsPrivateKeyFile *K8sCertFileMeta`
    *   `TlsCipherSuites []string`
    *   `FeatureGates *string`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.