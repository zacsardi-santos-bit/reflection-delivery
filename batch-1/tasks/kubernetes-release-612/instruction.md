Implement a function to compute the correct dependency list for the kubeadm package based on the target Kubernetes version. This function should return a comma-separated string of dependencies, varying the list according to the specified Kubernetes version.

*   Implement `getKubeadmDependencies(v version) (string, error)` in `debian/build.go`.
    *   Accept a `version` struct with a `Version` string field.
    *   Return a dependency string and an error.
*   Ensure the function returns no error for any valid Kubernetes version string.
*   Format the returned dependency string as a list of entries joined by ', ' (comma followed by a space).
*   For Kubernetes versions older than 1.11.0:
    *   Return exactly 4 dependencies in this order:
        *   'kubelet (>= 1.6.0)'
        *   'kubectl (>= 1.6.0)'
        *   'kubernetes-cni (= 0.6.0)'
        *   '${misc:Depends}'
*   For Kubernetes versions 1.11.0 and newer:
    *   Return the same 4 dependencies as for older versions.
    *   Append 'cri-tools (>= 1.11.0)' at the end, making a total of 5 entries.
*   Preserve the exact order of dependency entries in the returned string.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.