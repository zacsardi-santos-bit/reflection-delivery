I'm working on the Argo CD application controller and I've run into a frustrating bug.

*   The GetClusterServersByName function must not call IsInClusterEnabled() (via GetSettings) when the requested cluster name is not the special in-cluster cluster name; the in-cluster enablement check must only be performed when the requested name is the reserved in-cluster cluster name.

*   When GetClusterServersByName is called with the in-cluster cluster name and the ArgoCD configuration map is absent, the function must return an error.

*   When GetClusterServersByName is called with a non-in-cluster cluster name, the function must succeed and return the matching server URL(s) even when the ArgoCD configuration map (argocd-cm) is absent.

*   The getUpdatedClusterInfo method must count applications whose destination server URL matches the cluster's server URL; the result's ApplicationsCount field must be of type int64.

*   Applications that specify their destination by cluster name must also be counted if that name unambiguously resolves (via GetClusterServersByName) to the cluster's server URL.

*   When a cluster name maps to more than one cluster server URL (ambiguous name), applications targeting that cluster name must not be counted in ApplicationsCount for any specific cluster.

*   The GetDestinationServer function must resolve the cluster server URL for an ApplicationDestination: return the normalized server URL directly for server-based destinations, call GetClusterServersByName and return the single result for name-based destinations, and return an error if the name is ambiguous or missing.


*   Interface details: Type: Function
Name: GetClusterServersByName
Location: util/db/cluster.go
Signature: GetClusterServersByName(ctx context.Context, name string) ([]string, error)
Description: Returns the list of cluster server URLs that match the given cluster name. Must implement lazy evaluation: for names other than the reserved in-cluster cluster name, it must NOT call the settings manager to check whether in-cluster mode is enabled. The in-cluster enablement check must only be performed when the requested name is the in-cluster cluster name (appv1.KubernetesInClusterName constant). This allows the function to succeed for normal cluster name lookups even when the ArgoCD configuration map (argocd-cm) is absent.

Type: Method
Name: getUpdatedClusterInfo
Location: controller/clusterinfoupdater.go
Signature: (u *clusterInfoUpdater) getUpdatedClusterInfo(ctx context.Context, apps []*v1alpha1.Application, cluster v1alpha1.Cluster, clusterInfo *appv1.ClusterInfo, now metav1.Time) (struct with ApplicationsCount int64 field)
Description: Returns updated cluster information. The returned value's ApplicationsCount field (int64) counts applications whose destination targets this specific cluster. An app is counted if its destination server URL matches the cluster's server URL, or if its destination cluster name unambiguously resolves to the cluster's server URL via GetClusterServersByName. If GetClusterServersByName returns multiple server URLs for a destination name (ambiguous name), no application using that name is counted for this cluster.

Type: Function
Name: GetDestinationServer
Location: util/argo/argo.go
Signature: GetDestinationServer(ctx context.Context, destination argoappv1.ApplicationDestination, db ClusterGetter) (string, error)
Description: Resolves the destination cluster server URL from an ApplicationDestination without fetching the full Cluster object. For server-based destinations, returns the normalized server URL directly. For name-based destinations, calls GetClusterServersByName and returns the single matching server URL, or an error if the name is ambiguous or missing. This is used by getUpdatedClusterInfo to efficiently determine destination server URLs.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.