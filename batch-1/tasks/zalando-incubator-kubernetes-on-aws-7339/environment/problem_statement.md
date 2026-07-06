## Description

As part of upgrading the Kubernetes cluster infrastructure to version 1.29, the cluster configuration files need to be updated. Two specific changes are required:

1. Machine image (AMI) entries for Kubernetes version 1.29 must be added to the shared cluster defaults configuration and to the master node pool stack configuration, so that nodes can be provisioned with the correct base images for this version.
2. An explicit feature gate configuration entry that was previously needed to opt into the time zone–aware job scheduling feature must be removed, because that feature graduated to stable in Kubernetes 1.29 and is now always active — keeping the entry in the configuration is unnecessary and potentially misleading.

## Expected Behavior

- The cluster defaults configuration includes machine image entries specific to Kubernetes version 1.29.
- The master node pool stack configuration references the Kubernetes 1.29 machine images.
- The configuration entry explicitly enabling the time zone-aware job scheduling feature gate is absent from the cluster defaults, since the feature no longer needs to be manually enabled starting with Kubernetes 1.29.

## Why This Matters

Keeping configuration files aligned with the target Kubernetes version ensures nodes boot from the correct images and that stale feature gate flags do not cause confusion or compatibility issues during upgrades. Leaving behind outdated feature gate entries can also cause problems if Kubernetes later rejects unknown or removed feature gate names.
