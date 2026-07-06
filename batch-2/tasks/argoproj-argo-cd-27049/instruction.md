I'm seeing an issue in Argo CD where looking up a cluster by its friendly name fails unnecessarily.

*   The GetClusterServersByName function must NOT invoke the in-cluster-enabled check when the cluster name being looked up is anything other than the special in-cluster identifier. A lookup for any ordinary named cluster must succeed even if the ArgoCD configuration resource is absent.

*   When GetClusterServersByName is called with the in-cluster identifier as the name, it MUST invoke the in-cluster-enabled check. If that check fails (e.g., the configuration resource is missing), the function must return an error rather than silently ignoring or warning about the failure.

*   The getUpdatedClusterInfo method on clusterInfoUpdater must count applications whose destination server URL matches the cluster's server URL, as well as applications whose destination name resolves to the cluster's server URL via a database lookup. Both forms of destination must contribute to ApplicationsCount.

*   When a cluster name resolves to more than one server URL (the name is ambiguous), getUpdatedClusterInfo must not count applications that target the cluster using that ambiguous name; those applications contribute 0 to ApplicationsCount.

*   The clusterInfoUpdater struct must expose a db field (a database interface) and a namespace field (string) used to construct instances in tests.

*   The result returned by getUpdatedClusterInfo must include an ApplicationsCount field of type int64 reflecting the number of applications assigned to the cluster.


*   Interface details: Type: Function
Name: GetClusterServersByName
Location: util/db/cluster.go
Signature: GetClusterServersByName(ctx context.Context, name string) ([]string, error)
Description: Returns the list of server URLs for all clusters with the given name. Must only call IsInClusterEnabled() when the name equals the in-cluster identifier (appv1.KubernetesInClusterName). For all other names, the function must return results without invoking the in-cluster-enabled check. When the in-cluster-enabled check is invoked and returns an error, that error must be propagated to the caller (not swallowed or warned).

Type: Struct
Name: clusterInfoUpdater
Location: controller/clusterinfoupdater.go
Description: Holds state for updating cluster information. The struct must have a db field (a database/cluster-getter interface) and a namespace field (string). Tests instantiate it directly as &clusterInfoUpdater{db: ..., namespace: ...}.

Type: Method
Name: getUpdatedClusterInfo
Location: controller/clusterinfoupdater.go
Signature: getUpdatedClusterInfo(ctx context.Context, apps []*v1alpha1.Application, cluster v1alpha1.Cluster, clusterInfo *v1alpha1.ClusterInfo, now metav1.Time) *v1alpha1.ClusterInfo
Description: Returns updated cluster info including an ApplicationsCount int64 field. Apps whose Destination.Server matches the cluster's Server, or whose Destination.Name resolves (via DB lookup) to the cluster's Server, are counted. If a Destination.Name resolves to multiple server URLs (ambiguous), those apps are excluded from the count. The returned value's ApplicationsCount field must be of type int64.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.