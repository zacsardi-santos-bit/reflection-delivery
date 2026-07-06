Update the `inspectModule` function to correctly handle Terraform module paths that include subdirectory specifications using a double-slash separator. Ensure the function can load the module configuration from the specified subdirectory when present.

*   Modify the `inspectModule` function signature to:
    *   Accept `workingDir string` and `recipe *recipes.EnvironmentDefinition` as parameters.
*   Implement logic to handle the `TemplatePath` field in `EnvironmentDefinition`:
    *   If `TemplatePath` contains a '//' separator, extract the subdirectory path after '//' and append it to the module directory path.
    *   If `TemplatePath` does not contain '//' separator, use the `recipe.Name` as the module directory name within the root subdirectory of `workingDir`.
*   Ensure `inspectModule` can:
    *   Load the module configuration from the correct subdirectory when `TemplatePath` specifies one.
    *   Return a `moduleInspectResult` with `ContextVarExists=false`, `RequiredProviders=['aws']`, `ResultOutputExists=false`, and `Parameters={}` for a recipe with `Name='test-submodule'` and `TemplatePath='test-submodule//submodule'`.
    *   Return an error with a message containing 'error loading the module' if the module directory or submodule path does not exist or fails to load.
*   Set up the test data directory:
    *   Ensure `pkg/recipes/terraform/testdata/.terraform/modules/test-submodule/submodule/` contains a valid Terraform configuration file `main.tf` declaring the AWS provider (source "hashicorp/aws", version ">=3.0").

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.