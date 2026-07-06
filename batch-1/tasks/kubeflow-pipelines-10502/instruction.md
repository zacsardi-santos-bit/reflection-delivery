Update the CEL expression evaluator in the kubeflow/pipelines backend to support numeric comparisons between floating-point parameter values and integer literals. Ensure the system adheres to the CEL language specification for numeric type promotion.

*   Upgrade the cel-go library dependency:
    *   Modify `go.mod` to change the version of `github.com/google/cel-go` from `v0.9.0` to `v0.12.6`.
    *   Update `go.sum` to reflect the new hash entries for `github.com/google/cel-go v0.12.6`.

*   Ensure the following behaviors after the upgrade:
    *   A pipeline parameter stored as a double (e.g., 1.0) should correctly compare against an integer literal (e.g., `== 1`) and evaluate to `true`.
    *   The CEL condition `inputs.parameter_values['num'] == 1` should return `true` when the parameter is stored as a double, rather than a 'no such overload' error.
    *   The expression package should correctly handle struct field selection, including accessing fields by name and returning meaningful results for non-existent fields.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.