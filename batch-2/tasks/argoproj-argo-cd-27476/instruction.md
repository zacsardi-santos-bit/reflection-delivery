I'm hitting an issue where cluster operations in Argo CD fail when the main system secret doesn't have its cryptographic key field populated.

*   Must implement IsInClusterEnabled() (bool, error) on SettingsManager in util/settings/settings.go. This method must read the in-cluster enabled setting directly from the argocd-cm ConfigMap, without loading full settings and without depending on the server.secretkey field in the argocd-secret.

*   IsInClusterEnabled() must return (true, nil) when the ConfigMap exists but the cluster.inClusterEnabled key is absent (default value is true).

*   IsInClusterEnabled() must return (true, error) when the argocd-cm ConfigMap does not exist; the error message must contain the text 'configmap "argocd-cm" not found'.

*   IsInClusterEnabled() must return (false, nil) when cluster.inClusterEnabled is explicitly set to "false" in argocd-cm.

*   IsInClusterEnabled() must return (true, nil) when cluster.inClusterEnabled is explicitly set to "true" in argocd-cm.

*   The cluster database operations (CreateCluster, ListClusters, WatchClusters, GetClusterServersByName) in util/db/cluster.go must use IsInClusterEnabled() to check whether in-cluster is enabled, rather than loading full settings via GetSettings(). This makes them succeed even when server.secretkey is absent from the argocd-secret.

*   db.CreateCluster must succeed (return no error) for both in-cluster and external server addresses when server.secretkey is absent from the argocd-secret, as long as in-cluster is not explicitly disabled.

*   db.CreateCluster must return an error containing the text 'in-cluster has been disabled' when cluster.inClusterEnabled is set to 'false', even when server.secretkey is absent.

*   db.ListClusters must succeed (return no error) when server.secretkey is absent, and must include both explicit external cluster secrets and the implicit in-cluster entry in the returned list.

*   db.GetClusterServersByName with name 'in-cluster' must return (["https://kubernetes.default.svc"], nil) — no error — when server.secretkey is absent and in-cluster is not explicitly disabled.

*   db.WatchClusters must complete successfully and emit a cluster-added event for the local in-cluster address when server.secretkey is absent from the argocd-secret.


*   Interface details: Type: Method
Name: IsInClusterEnabled
Location: util/settings/settings.go
Signature: (mgr *SettingsManager) IsInClusterEnabled() (bool, error)
Description: Returns whether the in-cluster Kubernetes API server address is enabled. Reads directly from the argocd-cm ConfigMap without loading the full settings (no dependency on server.secretkey). Returns (true, nil) when the config map exists but the key is not set (default enabled). Returns (true, error) when the config map cannot be found — the error message must contain the underlying Kubernetes not-found message. Returns (false, nil) when cluster.inClusterEnabled is explicitly set to "false". Returns (true, nil) when cluster.inClusterEnabled is explicitly set to "true".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.