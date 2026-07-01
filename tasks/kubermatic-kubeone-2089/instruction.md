Implement the logic to include Cilium-specific kernel settings in node provisioning scripts when Cilium is configured as the CNI in a KubeOne cluster. Ensure these settings are applied across all supported Linux distributions without affecting other CNI configurations.

*   Extend the following functions to pass a `CILIUM` boolean flag in their template data map:
    *   `KubeadmDebian(cluster *kubeoneapi.KubeOneCluster, force bool) (string, error)` in `pkg/scripts/os_debian.go`
    *   `KubeadmCentOS(cluster *kubeoneapi.KubeOneCluster, force bool) (string, error)` in `pkg/scripts/os_centos.go`
    *   `KubeadmAmazonLinux(cluster *kubeoneapi.KubeOneCluster, force bool) (string, error)` in `pkg/scripts/os_amzn.go`
    *   `KubeadmFlatcar(cluster *kubeoneapi.KubeOneCluster) (string, error)` in `pkg/scripts/os_flatcar.go`
    *   The `CILIUM` flag should be set to true when `cluster.ClusterNetwork.CNI != nil && cluster.ClusterNetwork.CNI.Cilium != nil`.

*   Implement the internal helper function `ciliumCNI(cluster *kubeoneapi.KubeOneCluster) bool` in `pkg/scripts/os.go` to return true if Cilium is configured as the CNI.

*   Update the `sysctl-k8s` template in `pkg/scripts/funcs.go` to:
    *   Accept a data context (passed via ".").
    *   Conditionally include a Cilium sysctl override block when `.CILIUM` is true.
    *   Write the file `/etc/sysctl.d/99-zzz-override_cilium.conf` with:
        *   Comment: `# Disable rp_filter on ALL interfaces since it may cause mangled packets to be dropped`
        *   Reference: `# https://github.com/cilium/cilium/blob/v1.11.1/pkg/datapath/loader/base.go#L244`
        *   Setting: `net.ipv4.conf.all.rp_filter = 0`
    *   Ensure this block appears after writing `/etc/sysctl.d/k8s.conf` but before executing `sudo sysctl --system`.

*   Create and update golden test files in `pkg/scripts/testdata/` to reflect expected script outputs for Cilium-based clusters:
    *   `TestKubeadmDebian-cilium_cluster.golden`
    *   `TestKubeadmCentOS-cilium_cluster.golden`
    *   `TestKubeadmAmazonLinux-with_cilium.golden`
    *   `TestKubeadmFlatcar-with_cilium.golden`
    *   Each file must include the Cilium sysctl override block between the k8s.conf write and the `sudo sysctl --system` call.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.