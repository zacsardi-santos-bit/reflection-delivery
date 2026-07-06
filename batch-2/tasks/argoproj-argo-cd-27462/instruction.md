I'm running into a problem where several cluster management operations in Argo CD break when the main ArgoCD secret is missing its encryption key.

*   IsInClusterEnabled must read the in-cluster enabled flag directly from the argocd-cm ConfigMap, without depending on the ArgoCD secret or its server.secretkey field

*   When the argocd-cm ConfigMap does not exist, IsInClusterEnabled must return true (the default value) and an error whose message contains 'configmap "argocd-cm" not found'

*   When the argocd-cm ConfigMap exists but has no cluster.inClusterEnabled key, IsInClusterEnabled must return true and nil error

*   When cluster.inClusterEnabled is set to 'false' in the argocd-cm ConfigMap, IsInClusterEnabled must return false and nil error

*   When cluster.inClusterEnabled is set to 'true' in the argocd-cm ConfigMap, IsInClusterEnabled must return true and nil error

*   CreateCluster must succeed (no error) for both in-cluster and external clusters when the ArgoCD secret is missing server.secretkey

*   When creating an in-cluster cluster and in-cluster is explicitly disabled (cluster.inClusterEnabled set to 'false' in argocd-cm), CreateCluster must return an error whose message contains 'in-cluster has been disabled'

*   ListClusters must succeed and return all clusters, including the implicit in-cluster entry, when the ArgoCD secret is missing server.secretkey

*   GetClusterServersByName must return ['https://kubernetes.default.svc'] without error when queried with the name 'in-cluster' and the ArgoCD secret is missing server.secretkey

*   WatchClusters must function without returning an error when the ArgoCD secret is missing server.secretkey; it must deliver an add event for the implicit in-cluster with the old cluster as nil and the new cluster's server address equal to the Kubernetes internal API server address


*   Interface details: Type: Method
Name: IsInClusterEnabled
Location: util/settings/settings.go
Signature: IsInClusterEnabled() (bool, error)
Description: Returns whether the in-cluster server address is enabled by reading directly from the argocd-cm ConfigMap — independent of the ArgoCD secret and any encryption key. Returns (true, error) wrapping the original Kubernetes error if the ConfigMap cannot be read (e.g., if it does not exist, the error message must contain 'configmap "argocd-cm" not found'). Returns (true, nil) if the ConfigMap exists but the cluster.inClusterEnabled key is absent. Returns (false, nil) if cluster.inClusterEnabled is explicitly set to "false". Returns (true, nil) if cluster.inClusterEnabled is set to any other value (including "true"). This method must NOT call GetSettings() internally.

Type: Method
Name: ListClusters
Location: util/db/cluster.go
Signature: ListClusters(ctx context.Context) (*appv1.ClusterList, error)
Description: Lists all clusters, including the implicit in-cluster entry. Must call IsInClusterEnabled() instead of GetSettings() to check in-cluster status. If IsInClusterEnabled() returns an error, must log a warning and continue rather than returning an error, so the operation succeeds even when server.secretkey is missing from the ArgoCD secret.

Type: Method
Name: CreateCluster
Location: util/db/cluster.go
Signature: CreateCluster(ctx context.Context, c *appv1.Cluster) (*appv1.Cluster, error)
Description: Creates a cluster. Must call IsInClusterEnabled() instead of GetSettings() when checking whether in-cluster creation is allowed. If IsInClusterEnabled() returns an error, must log a warning and continue. If IsInClusterEnabled() returns false (in-cluster explicitly disabled), must return an error whose message contains "in-cluster has been disabled". Must succeed for both in-cluster and external clusters when server.secretkey is missing from the ArgoCD secret.

Type: Method
Name: WatchClusters
Location: util/db/cluster.go
Signature: WatchClusters(ctx context.Context, handleAddEvent func(cluster *appv1.Cluster), handleModEvent func(oldCluster *appv1.Cluster, newCluster *appv1.Cluster), handleDeleteEvent func(clusterServer string)) error
Description: Watches cluster changes and fires event callbacks. Must call IsInClusterEnabled() instead of GetSettings(). If IsInClusterEnabled() returns an error, must log a warning and continue rather than returning an error. Must work when server.secretkey is missing from the ArgoCD secret.

Type: Method
Name: GetClusterServersByName
Location: util/db/cluster.go
Signature: GetClusterServersByName(ctx context.Context, name string) ([]string, error)
Description: Returns the server addresses for clusters with a given name. Must call IsInClusterEnabled() instead of GetSettings() when checking in-cluster status. If IsInClusterEnabled() returns an error, must log a warning and continue rather than returning an error, so the operation succeeds even when server.secretkey is missing.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.