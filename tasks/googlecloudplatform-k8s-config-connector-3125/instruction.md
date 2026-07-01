Implement partial-update semantics for BigQuery Dataset updates and specify content preservation during deletions. Update the test infrastructure to simplify HTTP log normalization and modify callback signatures.

*   Update BigQuery Dataset resource handling:
    *   Use the PATCH HTTP method for updates, including only mutable fields.
    *   Exclude read-only fields from the PATCH request body.
    *   Include the query parameter `deleteContents=false` in DELETE requests.
    *   Write the `resourceID` back into the spec after successful reconciliation.
    *   Ensure `defaultCollation` is included in both PATCH requests and exported manifests.
    *   Report `externalRef` and `observedState.location` in the resource status.

*   Modify test infrastructure:
    *   Update `NormalizeHTTPLog` function in `tests/e2e/normalize.go` to accept six parameters: `(t *testing.T, events test.LogEntries, project testgcp.GCPProject, uniqueID string, folderID string, organizationID string)`.
    *   Remove `mockgcpregistry.Normalizer` parameter from `normalizeHTTPResponses` and `NormalizeHTTPLog`.
    *   Ensure `normalizeHTTPResponses` adds `.subnetworks` to slices sorted during normalization.
    *   Change `objectWalker.ReplacePath` method to accept `(path string, v string)` with `v` as a string.
    *   Update `JSONMutator` type to `func(obj map[string]any)`.
    *   Adjust `PrettifyJSON` methods to drop the `requestURL` parameter.

*   Normalize test scenarios:
    *   Use `alpha.cnrm.cloud.google.com/reconciler: direct` annotation for BigQuery Dataset set/unset field management scenarios.
    *   Normalize `.access[].userByEmail` values to `'user@google.com'`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.