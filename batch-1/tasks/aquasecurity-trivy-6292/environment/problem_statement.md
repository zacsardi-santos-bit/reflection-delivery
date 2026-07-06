## Description

The Terraform plan scanner fails when it encounters plan files where resource attribute values contain template-like interpolation expressions — strings that use a dollar sign or percent sign followed by curly braces, which is standard Terraform syntax for variable references and template directives. These strings are valid in real-world Terraform plans (they represent unresolved variable references at plan time), but they currently cause the scanner to crash or produce errors instead of scanning and evaluating the resources normally.

## Expected Behavior

- When a Terraform plan JSON file is scanned and a resource attribute (e.g., an S3 bucket name) has a value like a template expression, the scan should complete without error.
- The scanner should correctly detect policy violations against those resources even when their attribute values contain template-style syntax.
- The scan results should contain the expected number of failures and correctly report rule metadata (such as the AVD ID) from the associated policy.

## Why This Matters

Real Terraform plans often include unresolved variable references in planned resource values. If the scanner cannot handle these strings, it will silently fail or crash for a significant subset of real-world plans, making security scanning unreliable for any team that uses variable interpolation in their Terraform configuration.
