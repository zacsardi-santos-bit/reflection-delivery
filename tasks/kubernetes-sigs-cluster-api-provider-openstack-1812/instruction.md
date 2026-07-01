Create the necessary kustomize configuration for the new API version's Flatcar system-extension scenario to ensure the CI build succeeds and all unit tests can run. Use the existing setup for the previous API version as a reference.

*   Create a directory at `kustomize/v1alpha8/flatcar-sysext/`.
*   Ensure the directory contains:
    *   A `kustomization.yaml` file with:
        *   `apiVersion: kustomize.config.k8s.io/v1beta1`
        *   `kind: Kustomization`
        *   A resource reference to `../default`
        *   At least one patch file reference
    *   A patch YAML file with:
        *   OpenStack resource definitions using `apiVersion: infrastructure.cluster.x-k8s.io/v1alpha8`
        *   Definitions for at least `OpenStackCluster` and `OpenStackMachineTemplate` kinds
*   Verify that running `kustomize build test/e2e/data/kustomize/flatcar-sysext` exits with code 0.
*   Ensure all unit tests under `./api/...` and `./pkg/...` pass after the kustomize build step is successful.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.