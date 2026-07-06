Create a new Go module configuration to enable self-contained integration tests for the Elasticsearch Go client. Ensure the module is correctly set up to manage containerized Elasticsearch instances automatically during tests.

*   Create a Go module manifest at `internal/testing/go.mod` with the following specifications:
    *   Name the module "testing" to ensure the import path "testing/containertest" resolves correctly.
    *   Declare `github.com/testcontainers/testcontainers-go` as a direct dependency.
    *   Declare `github.com/testcontainers/testcontainers-go/modules/elasticsearch` as a direct dependency.
    *   Include a replace directive for `github.com/elastic/go-elasticsearch/v8` pointing to the local repository root (`../../`).

*   Generate a `internal/testing/go.sum` file:
    *   Ensure it contains the correct cryptographic hashes for all declared dependencies and their transitive requirements.

*   Verify the integration tests:
    *   Ensure tests in `internal/testing/e2e` compile successfully using the build tag 'integration'.
    *   Run tests with `go test` from the `internal/testing` directory and confirm they pass.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.