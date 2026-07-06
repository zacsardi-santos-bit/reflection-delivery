Implement a solution to ensure the Terraform plan JSON scanner can handle resource attribute values containing template-like expressions without crashing. Update the scanner to correctly detect policy violations even when these expressions are present.

*   Update the Terraform plan JSON scanner to:
    *   Successfully scan plan files with resource attribute values containing template-like expressions ('${...}' or '%{...}'), ensuring no errors are returned from ScanFS.
    *   Return exactly 1 failure in the scan results when a resource attribute value is a template-like string and a matching Rego policy rule exists.
    *   Ensure the failed scan result's rule AVDID is 'AVD-TEST-0123' if specified in the Rego policy metadata.

*   Ensure the existence of a test data file:
    *   Path: `pkg/iac/scanners/terraformplan/tfjson/test/testdata/plan_with_template.json`
    *   Content: A valid Terraform plan JSON where the S3 bucket name value is the template-style string '${template-name-is-$evil}'.

*   Modify the string rendering logic in `pkg/iac/terraform/resource_block.go`:
    *   Implement the function `parseStringPrimitive(input string) string` to escape Terraform template expressions.
        *   Double the leading '$' or '%' character in substrings matching the pattern '($|%)\{.+\}'.
        *   For multi-line strings, return a heredoc literal.
        *   For single-line strings, return a quoted string literal.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.