## Description

When using topology-based cluster management to orchestrate Kubernetes version upgrades, there is currently no enforcement preventing an operator from requesting a new version bump before the previous upgrade has fully completed. This means a second version change can be triggered while:

- The control plane is still provisioning or rolling out a version upgrade
- Worker machine deployments still have machines running an older version
- Machine pool nodes still report an older kubelet version

This creates a risk of overlapping upgrades that can leave the cluster in an inconsistent or destabilized state.

## Expected Behavior

- The cluster topology webhook should validate that all cluster components (control plane, machine deployments, machine pools) are fully converged at the current topology version before permitting a new version bump.
- If any component is still provisioning, still mid-upgrade, or not yet at the desired version, the update request should be rejected with a clear error.
- Operators who are aware of the risk and need to override this protection in emergency situations (e.g., a stuck upgrade) should be able to apply a special annotation to the cluster. When that annotation is present, the version bump should be permitted but a warning should be returned to inform the operator.

## Why This Matters

Without this guard, automated tools or operators could inadvertently queue up a second Kubernetes version upgrade on top of a still-running first upgrade. The validation makes the system safer by ensuring upgrades complete before new ones begin, while still allowing an escape hatch for edge cases.

## Additional Context

The upgrade-checking logic for machine deployments and machine pools should be extracted into a shared utility package so it can be reused across both the controller reconciliation path and the webhook validation path, rather than being duplicated.
