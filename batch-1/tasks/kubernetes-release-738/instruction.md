Update the Debian packaging logic for kubeadm to reflect the current support baseline for its dependencies. Ensure the configuration file path selection logic works correctly for all supported Kubernetes versions.

*   Implement the function `getKubeadmKubeletConfigFile(v version) (string, error)` in `debian/build.go`.
    *   Return the path "post-1.10/10-kubeadm.conf" for Kubernetes versions >= 1.12.0.
    *   Return an error and an empty string for invalid or non-semver version strings like 'not-a-real-version'.

*   Implement the function `getKubeadmDependencies(v version) (string, error)` in `debian/build.go`.
    *   For any supported Kubernetes version (>= 1.13.0), include the following dependencies:
        *   'kubelet (>= 1.13.0)'
        *   'kubectl (>= 1.13.0)'
        *   'kubernetes-cni (>= 0.7.5)'
        *   'cri-tools (>= 1.13.0)'
        *   '${misc:Depends}'
    *   Ensure the dependency list for version 1.13.0 contains exactly these five entries.
    *   Ensure the dependency list for version 1.15.0 contains the same five entries with the same version constraints.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.