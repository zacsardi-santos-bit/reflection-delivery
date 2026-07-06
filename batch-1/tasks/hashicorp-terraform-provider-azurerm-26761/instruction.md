Update the Terraform provider for Azure to use the current API version for the Hybrid Kubernetes connected clusters service. Replace all references to the outdated API version in the source files and vendor directory to restore compilation and functionality.

*   Update all source files in `internal/services/arckubernetes/`:
    *   Change imports to use `github.com/hashicorp/go-azure-sdk/resource-manager/hybridkubernetes/2024-01-01/connectedclusters` instead of `2021-10-01`.
    *   Affected files include:
        *   `arc_kubernetes_cluster_resource.go`
        *   `arc_kubernetes_cluster_extension_resource.go`
        *   `arc_kubernetes_flux_configuration_resource.go`
        *   `client/client.go`

*   Modify the vendor directory:
    *   Ensure the SDK package directory exists at `vendor/github.com/hashicorp/go-azure-sdk/resource-manager/hybridkubernetes/2024-01-01/connectedclusters/`.
    *   Replace or rename the old `2021-10-01/connectedclusters/` directory with the `2024-01-01` path.

*   Update the `version.go` file:
    *   Set the API version constant to `"2024-01-01"` in the `version.go` file inside the vendored `2024-01-01/connectedclusters` package.

*   Modify the `vendor/modules.txt` file:
    *   Reference `github.com/hashicorp/go-azure-sdk/resource-manager/hybridkubernetes/2024-01-01/connectedclusters` instead of the old `2021-10-01` path.

*   Ensure successful compilation:
    *   Verify that the `internal/services/arckubernetes` package compiles successfully.
    *   Confirm that unit tests in `internal/services/arckubernetes/validate` can be executed.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.