Consolidate the stack lookup process in the Terraform AWS provider by implementing a single function to find stacks by name. Replace all existing stack lookup calls with this new function to ensure consistent error handling when a stack is not found.

*   Implement the function `FindStackByName` in the `internal/service/cloudformation` package.
    *   Use the signature: `FindStackByName(ctx context.Context, conn *cloudformation.CloudFormation, name string) (*cloudformation.Stack, error)`.
    *   Return a valid `*cloudformation.Stack` pointer when the stack exists.
    *   Return an error satisfying `tfresource.NotFound()` when the stack does not exist or has been deleted.
*   Remove the existing `FindStackByID` function from the cloudformation package.
*   Replace all calls to `FindStackByID` with `FindStackByName` within the cloudformation service package.
*   Ensure the cloudformation package compiles successfully without any undefined symbol errors.
*   Verify that all existing unit tests in the cloudformation package pass, including those unrelated to stack lookup.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.