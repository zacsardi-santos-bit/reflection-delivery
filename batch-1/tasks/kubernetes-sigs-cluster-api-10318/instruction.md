Implement a safeguard in the cluster topology webhook to prevent initiating a new Kubernetes version upgrade while a previous one is still in progress. Ensure that all cluster components are fully converged at the current topology version before allowing a version bump, with an option to override this check in emergencies.

* Implement the following functions in the `internal/topology/check` package:
    * `IsMachineDeploymentUpgrading(ctx context.Context, c client.Reader, md *clusterv1.MachineDeployment) (bool, error)`: Return `true` if any Machine in the MachineDeployment has a different version than the MachineDeployment's version. Return `false` if all Machines match or if there are no Machines.
    * `IsMachinePoolUpgrading(ctx context.Context, c client.Reader, mp *expv1.MachinePool) (bool, error)`: Return `true` if any Node in the MachinePool's `Status.NodeRefs` has a different kubelet version than the MachinePool's desired version. Return `false` if all Nodes match or if there are no NodeRefs.

* Implement the following functions in the `internal/webhooks` package:
    * `validateTopologyControlPlaneVersion(ctx context.Context, c client.Reader, old *clusterv1.Cluster, oldVersion semver.Version) error`: Return an error if the control plane is provisioning, upgrading, or not at the topology version. Return `nil` if converged.
    * `validateTopologyMachineDeploymentVersions(ctx context.Context, c client.Reader, old *clusterv1.Cluster, oldVersion semver.Version) error`: List topology-owned MachineDeployments and return an error if any are not at the topology version or are upgrading. Return `nil` if none exist.
    * `validateTopologyMachinePoolVersions(ctx context.Context, c client.Reader, tracker ClusterCacheTrackerReader, old *clusterv1.Cluster, oldVersion semver.Version) error`: List topology-owned MachinePools and return an error if any are not at the topology version, are upgrading, or if Nodes cannot be fetched. Return `nil` if none exist.

* Update the `Cluster` webhook struct in `internal/webhooks/cluster.go`:
    * Add a `Tracker` field of type `ClusterCacheTrackerReader`.

* Ensure the cluster topology validation:
    * Blocks version bumps when the control plane, machine deployments, or machine pools are not fully converged.
    * Allows version bumps with a warning if the `clusterv1.ClusterTopologyUnsafeUpdateVersionAnnotation` is set.
    * Allows version bumps without errors or warnings when all components are fully at the current topology version.
    * Passes version checks without error if there are no topology-owned MachineDeployments or MachinePools.

* Refactor to use the new upgrade check functions, replacing the previous inline logic in `MachineDeploymentState`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.