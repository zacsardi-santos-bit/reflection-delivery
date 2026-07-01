## Description

The volume snapshot management code currently only supports the older alpha version of the Kubernetes VolumeSnapshot API. However, Kubernetes has since introduced a beta version of this API with a different structure, and many clusters are now running the beta version instead of — or alongside — the alpha version. As a result, snapshot operations silently fail or behave incorrectly on clusters that only have the beta API installed.

Additionally, the current factory function for creating a snapshotter always returns a working object even when no snapshot API is available on the cluster, making it impossible to detect this error condition early.

## Expected Behavior

- The system should detect at runtime which version of the VolumeSnapshot API is available on the cluster (alpha or beta) and automatically select the correct implementation.
- There should be separate, explicitly-constructable implementations for each API version, so callers can also instantiate a specific version directly if needed.
- The factory function should return an error if neither supported API version is available on the cluster, rather than silently returning a non-functional object.
- Helper functions to construct the unstructured API objects (snapshot, snapshot content, snapshot class) should be available for both API versions.
- The beta API version's types (including snapshot class and snapshot content) should be defined in a dedicated package, mirroring the structure of the existing alpha package.

## Why This Matters

Without this change, any cluster that has upgraded to the beta VolumeSnapshot API cannot use the snapshot functionality at all. Supporting both versions ensures compatibility across different Kubernetes versions and cluster configurations, and makes the system more robust by surfacing errors when no snapshot API is available.
