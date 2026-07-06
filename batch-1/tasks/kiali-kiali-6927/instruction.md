Implement a feature in Kiali to support the configuration of inaccessible clusters so they appear in the cluster listing. Ensure that each cluster in the response includes a flag indicating its accessibility status and any configured Kiali URLs. Update method signatures and configurations to support this functionality.

*   Update the `GetClusters` method in `business/mesh.go`:
    *   Change the signature to `GetClusters() ([]kubernetes.Cluster, error)`.
    *   Ensure it returns clusters with their accessibility status and Kiali instances populated.
    *   Include clusters from `conf.Clustering.InaccessibleClusters` with `Accessible` set to false.
    *   Populate the `KialiInstances` field using `conf.Clustering.KialiURLs`.
    *   Avoid duplicate entries when a Kiali URL is configured for an already accessible cluster.
    *   Ensure functionality in namespaced access mode.

*   Modify the `Cluster` struct in `kubernetes/cluster_secret.go`:
    *   Add an `Accessible bool` field with JSON tag `accessible`.

*   Update the `KubeCache` interface in `kubernetes/cache/kube_cache.go`:
    *   Rename `GetServices` to `GetServicesBySelectorLabels(namespace string, selectorLabels map[string]string)`.
    *   Add a new `GetServices(namespace string, labelSelector string)` method.

*   Refactor the `NewRouter` function in `routing/router.go`:
    *   Accept configuration and dependencies as explicit parameters.

*   Refactor the `NewServer` function in `server/server.go`:
    *   Change the config parameter to a pointer `(*config.Config)`.
    *   Rename the tracing client loader parameter to `traceClientLoader`.

*   Update the `Config` struct in `config/config.go`:
    *   Add a `Clustering` field with `InaccessibleClusters []Cluster` and `KialiURLs []KialiURL`.
    *   Ensure backward compatibility with `conf.KialiFeatureFlags.Clustering.KialiURLs`.

*   Modify the `MeshCluster` TypeScript interface in `frontend/src/types/Mesh.ts`:
    *   Add a required `accessible: boolean` field.

*   Ensure the `/api/clusters` endpoint:
    *   Returns an HTTP 200 response with a JSON body of `kubernetes.Cluster` objects.
    *   Includes any configured inaccessible clusters.

*   Update the `Clusters` function in `tests/integration/utils/kiali/kiali_client.go`:
    *   Fetch the cluster list from the `/api/clusters` endpoint and return it as a slice of `kubernetes.Cluster`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.