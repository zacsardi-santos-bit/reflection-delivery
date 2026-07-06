Fix the CRD upgrade validation logic in the operator-lifecycle-manager catalog operator to prevent false positive validation errors. Ensure the schema validator receives the correct raw content of custom resources. Add test data files to cover the regression case for a PostgreSQL administration operator.

*   Update the `validateExistingCRs` function in `pkg/controller/operators/catalog/operator.go`:
    *   Extract the raw unstructured map content from each `runtime.Object` before passing it to the schema validator.
    *   Perform a type assertion to `*unstructured.Unstructured` and call `UnstructuredContent()` to obtain the `map[string]interface{}` value.
    *   Ensure the type assertion is done unconditionally before calling `validation.ValidateCustomResource`.

*   Ensure `validateV1Beta1CRDCompatibility` behaves correctly:
    *   When called with identical `oldCRD` and `newCRD`, it must return no error even if existing CRs contain large integer values.

*   Create test data files for regression testing:
    *   `pkg/controller/operators/catalog/testdata/postgrestolerations/crd.yaml`:
        *   Define a `v1beta1` CustomResourceDefinition for the PGAdmin resource.
        *   Include metadata and a full OpenAPI v3 schema.
    *   `pkg/controller/operators/catalog/testdata/postgrestolerations/pgadmin.cr.yaml`:
        *   Define a PGAdmin custom resource with `apiVersion` `postgres-operator.crunchydata.com/v1beta1`.
        *   Include a `tolerations` entry with `tolerationSeconds: 1726856593000774400`.

*   Ensure all existing subtests for CRD compatibility validation continue to pass without modification.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.