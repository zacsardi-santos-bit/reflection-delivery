I'm working on the network driver operator and I've found a bug in how IP address pools are auto-created at startup.

*   The autoCreatePools function must perform a two-pass approach: first iterate over the entire poolMap and validate every spec, collecting valid pool objects; if any spec fails to parse, return the error immediately without creating any pools.

*   After all specs are validated successfully, autoCreatePools must create each pool via the Kubernetes API; if a pool already exists (AlreadyExists error) or any other create error occurs, log the error and continue — do not return an error.

*   The driverNodeConfigOps.Update method must GET the existing CiliumNetworkDriverNodeConfig from Kubernetes; if the GET fails with a non-NotFound error, it must propagate that error to the caller.

*   The driverNodeConfigOps.Update method must skip calling the Kubernetes update API when the existing spec on the K8s object already equals the desired spec (i.e., the operation is idempotent with respect to unchanged specs).

*   The driverNodeConfigOps.Delete method must propagate any delete errors that are not NotFound errors.

*   The driverNodeConfigOps.Prune method must list all CiliumNetworkDriverNodeConfig objects from Kubernetes; if the List call fails, it must propagate the error.

*   The driverNodeConfigOps.Prune method must delete any K8s CiliumNetworkDriverNodeConfig object whose name does not appear in the StateDB iterator; if a delete fails, it must propagate the error.

*   The driverClusterConfigOps.Update method must GET the existing CiliumNetworkDriverClusterConfig from Kubernetes; if the GET fails with a non-NotFound error, it must propagate that error to the caller.

*   The driverClusterConfigOps.Update method must skip calling UpdateStatus when the conflict condition on the K8s object already reflects the desired state: skip when IsConflicting is false and no conflict condition is present, and skip when IsConflicting is true and the condition type NetworkDriverClusterConfigConditionConflict with status ConditionTrue and reason NetworkDriverClusterConfigReasonConflict is already set.

*   The registerDriverNodeConfigReconciler function must return nil without registering any reconciler when the Kubernetes clientset is disabled (cs.IsEnabled() == false) or when cfg.EnableCiliumNetworkDriver is false.

*   The registerDriverClusterConfigReconciler function must return nil without registering any reconciler when the Kubernetes clientset is disabled (cs.IsEnabled() == false) or when cfg.EnableCiliumNetworkDriver is false.

*   The registerAllocator function must be a no-op (no panic, no registration) when the Kubernetes clientset is disabled or when AllocatorParams.DaemonCfg.EnableCiliumNetworkDriver is false.

*   The ciliumResourceIPPool function must return (nil, nil) when the Kubernetes clientset is disabled.


*   Interface details: Type: Function
Name: autoCreatePools
Location: operator/pkg/networkdriver/ipam/ipam.go
Signature: autoCreatePools(ctx context.Context, client cilium_v2alpha1.CiliumResourceIPPoolInterface, poolMap map[string]string, logger *slog.Logger) error
Description: Creates CiliumResourceIPPool objects from a map of pool names to spec strings. Must perform a two-pass approach: first validate all specs, return an error immediately (without creating anything) if any spec is invalid; then create all validated pools, logging (but not returning) AlreadyExists or other create errors.

Type: Function
Name: ciliumResourceIPPool
Location: operator/pkg/networkdriver/ipam/ipam.go
Signature: ciliumResourceIPPool(p any, cs k8sClient.Clientset, logger any) (any, error)
Description: Returns (nil, nil) when the Kubernetes client is disabled (cs.IsEnabled() == false).

Type: Function
Name: registerAllocator
Location: operator/pkg/networkdriver/ipam/ipam.go
Signature: registerAllocator(p AllocatorParams)
Description: Registers the IPAM allocator. Must be a no-op (no panic, no registration) when the Kubernetes client is disabled or when AllocatorParams.DaemonCfg.EnableCiliumNetworkDriver is false.

Type: Struct
Name: AllocatorParams
Location: operator/pkg/networkdriver/ipam/ipam.go
Description: Parameters for registerAllocator.
Fields:
  Clientset  k8sClient.Clientset
  DaemonCfg  *option.DaemonConfig

Type: Struct
Name: driverNodeConfigOps
Location: operator/pkg/networkdriver/config/nodeconfig.go
Description: Implements reconciler operations for CiliumNetworkDriverNodeConfig Kubernetes objects.
Fields:
  client  (CiliumNetworkDriverNodeConfigsGetter interface, obtained via cs.CiliumV2alpha1().CiliumNetworkDriverNodeConfigs())
Methods:
  Update(ctx context.Context, txn any, rev uint64, obj *driverNodeConfig) error
  Delete(ctx context.Context, txn any, rev uint64, obj *driverNodeConfig) error
  Prune(ctx context.Context, txn any, iter func(yield func(*driverNodeConfig, uint64) bool)) error

Type: Struct
Name: driverNodeConfig
Location: operator/pkg/networkdriver/config/nodeconfig.go
Description: Internal StateDB record representing a desired node-level network driver configuration.
Fields:
  Node    string
  Config  *cilium_v2alpha1_api.CiliumNetworkDriverNodeConfigSpec

Type: Struct
Name: driverClusterConfigOps
Location: operator/pkg/networkdriver/config/clusterconfig.go
Description: Implements reconciler operations for CiliumNetworkDriverClusterConfig Kubernetes objects.
Fields:
  client  (CiliumNetworkDriverClusterConfigsGetter interface, obtained via cs.CiliumV2alpha1().CiliumNetworkDriverClusterConfigs())
Methods:
  Update(ctx context.Context, txn any, rev uint64, obj *driverClusterConfig) error

Type: Struct
Name: driverClusterConfig
Location: operator/pkg/networkdriver/config/clusterconfig.go
Description: Internal StateDB record representing a desired cluster-level network driver configuration.
Fields:
  Name           string
  IsConflicting  bool

Type: Function
Name: registerDriverNodeConfigReconciler
Location: operator/pkg/networkdriver/config/nodeconfig.go
Signature: registerDriverNodeConfigReconciler(params reconciler.Params, db any, table any, cfg *option.DaemonConfig, cs k8sClient.Clientset) error
Description: Registers the node config reconciler. Must return nil without registering anything when cs.IsEnabled() is false or cfg.EnableCiliumNetworkDriver is false.

Type: Function
Name: registerDriverClusterConfigReconciler
Location: operator/pkg/networkdriver/config/clusterconfig.go
Signature: registerDriverClusterConfigReconciler(params reconciler.Params, db any, table any, cfg *option.DaemonConfig, cs k8sClient.Clientset) error
Description: Registers the cluster config reconciler. Must return nil without registering anything when cs.IsEnabled() is false or cfg.EnableCiliumNetworkDriver is false.

Type: Constant
Name: NetworkDriverClusterConfigConditionConflict
Location: pkg/k8s/apis/cilium.io/v2alpha1/
Description: String constant used as the Type field in a metav1.Condition to indicate a conflict among CiliumNetworkDriverClusterConfig objects.

Type: Constant
Name: NetworkDriverClusterConfigReasonConflict
Location: pkg/k8s/apis/cilium.io/v2alpha1/
Description: String constant used as the Reason field in a metav1.Condition to indicate a conflict among CiliumNetworkDriverClusterConfig objects.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.