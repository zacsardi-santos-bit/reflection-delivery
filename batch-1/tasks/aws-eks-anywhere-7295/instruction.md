Implement a feature to provide an explicit download URL for the etcd binary during node setup in clusters using external etcd with Ubuntu bootstrapping. Update the internal distribution metadata to include this URL and modify the configuration function to determine when to include it based on the cluster version.

*   Update the `KubeDistro` struct in `pkg/cluster/spec.go`:
    *   Add a new string field `EtcdURL` to store the download URL for the etcd amd64 binary archive.
    *   Populate this field by parsing the etcd component's asset list from EKS Distro release data, selecting the archive URI for the first 'amd64' entry.

*   Modify the `SetUbuntuConfigInEtcdCluster` function in `pkg/clusterapi/etcd.go`:
    *   Change the function signature to accept a `*cluster.VersionsBundle` and an `eksaVersion` string.
    *   Set `CloudInitConfig.Version` from `versionsBundle.KubeDistro.EtcdVersion`.
    *   If `eksaVersion` is `>= v0.19.0` (or the dev build version), set `EtcdReleaseURL` in `CloudInitConfig` using `GetExternalEtcdReleaseURL`. Otherwise, leave `EtcdReleaseURL` unset.

*   Add a new function `GetExternalEtcdReleaseURL` in `pkg/providers/common/common.go`:
    *   Signature: `GetExternalEtcdReleaseURL(clusterVersion string, versionBundle *cluster.VersionsBundle) (string, error)`.
    *   Return `versionBundle.KubeDistro.EtcdURL` for valid semver `>= v0.19.0` or the dev build version.
    *   Return `('', nil)` for valid semver below `v0.19.0`.
    *   Return `('', error)` for invalid semver with error message: `'invalid semver for clusterVersion: <underlying error>'`.

*   Implement `DevEksaVersion` in `internal/test/`:
    *   Return an `anywherev1.EksaVersion` value that enables the etcd URL feature.

*   Update test data structures for `VersionsBundle`:
    *   Include a populated `EtcdURL` field in `KubeDistro` in `pkg/providers/docker`, `pkg/providers/snow`, and `pkg/clusterapi`.
    *   Ensure this field is included when building `KubeDistro` from EKS Distro release metadata.

*   For vsphere controlplane YAML rendering:
    *   When `EksaVersion` enables the etcd URL feature, include an `etcdReleaseURL` field under `cloudInitConfig` in the rendered YAML output.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.