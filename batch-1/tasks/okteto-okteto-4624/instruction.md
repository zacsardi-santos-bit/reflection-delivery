Implement a CLI command to validate Okteto manifest files before deployment. Ensure the command provides instant feedback on the correctness of the manifest, identifying syntax errors, invalid values, and unknown configuration keys. Develop integration test helpers and correct a typo in an existing test case.

*   Define the `ValidateOptions` struct in the `integration/commands` package.
    *   Include fields: `Workdir` (string), `ManifestPath` (string), and `OktetoHome` (string).

*   Implement the `RunOktetoValidate` function in the `integration/commands` package.
    *   Signature: `RunOktetoValidate(oktetoPath string, opts *ValidateOptions) error`.
    *   Execute the Okteto CLI validate command using the provided options.
    *   Return `nil` if the CLI exits successfully, or a non-nil error if it exits with a non-zero status.

*   Ensure `RunOktetoValidate` behaves as follows:
    *   Returns `nil` when called with a valid manifest file path.
    *   Returns a non-nil error for invalid manifest files, including empty files, files with YAML syntax errors, files with invalid dev-section values, or files with unrecognized root-level properties.
    *   Returns a non-nil error when called with a non-existent file path.

*   Create test manifest fixture files in `integration/validate/manifests/`.
    *   Valid manifests (prefix 'valid-'): `valid-movies-rental.yml`, `valid-movies-frontend.yml`, `valid-go-getting-started.yml`, `valid-movies.yml`, `valid-aws-lambda-with-terraform.yml`, `valid-tacopshop-with-cloudflare.yml`, `valid-okteto-load-testing-with-artillery.yml`, `valid-movies-multi-repo.yml`, `valid-go-getting-started-chart.yml`, `valid-gcp-cloud-credentials.yml`.
    *   Invalid manifests (prefix 'invalid-'): `invalid-empty.yml`, `invalid-syntax.yml`, `invalid-dev.yml`, `invalid-non-existing-root-level-prop.yml`.

*   Correct the typo in `pkg/schema/schema_test.go`.
    *   Change the test case name from "okteto-community/tacoshop-with-cloudflare" to "okteto-community/tacopshop-with-cloudflare".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.