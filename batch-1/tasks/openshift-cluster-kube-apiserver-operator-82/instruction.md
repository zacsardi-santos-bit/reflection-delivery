Implement a mechanism to automatically retrieve secrets and configmaps from the Kubernetes API and write them to the local filesystem for a static pod deployment. Use the provided deployment revision identifier, namespace, and name prefixes to locate resources, and ensure they are stored in a structured directory layout.

*   Define the `InstallOptions` struct in the `pkg/cmd/installer` package with the following fields:
    *   `KubeClient kubernetes.Interface`
    *   `DeploymentID string`
    *   `Namespace string`
    *   `PodConfigMapNamePrefix string`
    *   `SecretNamePrefixes []string`
    *   `ConfigMapNamePrefixes []string`
    *   `ResourceDir string`
    *   `PodManifestDir string`

*   Implement the `copyContent` method on `InstallOptions` with the signature `(o *InstallOptions) copyContent() error`:
    *   Fetch each secret named `{prefix}-{DeploymentID}` for every prefix in `SecretNamePrefixes` from the configured `Namespace` using `KubeClient`.
    *   Write each key-value entry in the secret's `Data` map as a file at the path `{ResourceDir}/{PodConfigMapNamePrefix}-{DeploymentID}/secrets/{secretName}/{filename}`.
    *   Fetch each configmap named `{prefix}-{DeploymentID}` for every prefix in `ConfigMapNamePrefixes` from the configured `Namespace` using `KubeClient`.
    *   Write each key-value entry in the configmap's `Data` map as a file at the path `{ResourceDir}/{PodConfigMapNamePrefix}-{DeploymentID}/configmaps/{configmapName}/{filename}`.
    *   Fetch the pod configmap named `{PodConfigMapNamePrefix}-{DeploymentID}` from the configured `Namespace` using `KubeClient`.
    *   Retrieve the pod manifest from its `pod.yaml` key and write it to `{ResourceDir}/{PodConfigMapNamePrefix}-{DeploymentID}/{PodConfigMapNamePrefix}.yaml`.
    *   Also write the pod manifest content to `{PodManifestDir}/{PodConfigMapNamePrefix}.yaml` for static pod manager discovery.
    *   Ensure all intermediate directories required by the file paths are created if they do not exist.
    *   Return `nil` on success and a non-nil error if any Kubernetes API call or filesystem operation fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.