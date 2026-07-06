## Description

When clusters are configured with external etcd and nodes are bootstrapped using Ubuntu/cloud-init, the system configures the etcd bootstrapping tool with a version number but does not tell it where to download the etcd binary. This means that if the etcd binary is not already cached on the node, the bootstrapping process may fail because it has to locate the binary on its own.

We need to pass an explicit download URL for the etcd binary as part of the node bootstrapping configuration. This URL should be derived from the distribution release metadata already stored in the version bundle. The URL should only be set for newer cluster versions (at or above a specific release threshold), so that older clusters are not affected and do not trigger unintended etcd node restarts during an upgrade.

## Expected Behavior

- The internal data structure that tracks Kubernetes distribution information must carry a new field for the etcd binary download URL, populated from the distribution release metadata.
- The function responsible for configuring Ubuntu-based etcd clusters must be updated to accept the full version bundle (instead of just a version string) and the current cluster version, so it can determine whether to include the etcd download URL.
- A new utility function must be introduced to determine whether and what etcd download URL to use, based on the cluster version. Versions below the threshold return no URL; versions at or above the threshold return the URL from the version bundle. Invalid version strings must produce a descriptive error.
- The dev/unreleased build version must also be treated as eligible to receive the etcd URL.

## Why This Matters

Without a download URL, etcd bootstrapping relies on the binary being present in a cache or reachable through a default location — which is not guaranteed in all environments. Explicitly providing the URL makes bootstrapping more reliable and self-contained, especially in air-gapped or controlled network environments.
