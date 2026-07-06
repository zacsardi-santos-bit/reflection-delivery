Implement the necessary changes to support OpenEBS integration tests in the Talos integration test suite. Refactor the UserDisks function to simplify its API and create configuration patches for the test environment.

*   Refactor the UserDisks method in `internal/integration/base/api.go`:
    *   Change the method signature to `UserDisks(ctx context.Context, node string) []string`, returning only a `[]string`.
    *   Handle errors internally using the suite's built-in require/assertion mechanism instead of returning them.
*   Update all call sites of UserDisks within the integration test suite:
    *   Modify the calls to accommodate the new single-return-value signature by removing the error variable from the assignment.
*   Create configuration patch files for OpenEBS integration tests:
    *   Create `hack/test/patches/openebs-cp.yaml`:
        *   Configure cluster-level PodSecurity admission control to exempt the 'openebs' namespace.
        *   Use `apiVersion: pod-security.admission.config.k8s.io/v1beta1` and `kind: PodSecurityConfiguration`.
    *   Create `hack/test/patches/openebs.yaml`:
        *   Set the sysctl `vm.nr_hugepages` to '1024'.
        *   Add a node label `openebs.io/engine` with the value 'mayastor'.
        *   Configure a kubelet extra mount for `/var/local` as a bind mount with `rshared` and `rw` options.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.