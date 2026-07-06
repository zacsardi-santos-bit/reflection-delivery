Fix the service profile feature in the service mesh project to address issues with route conditions, response classification, and naming conventions. Update the CLI tooling to generate example service profile templates with the new conventions.

*   Update the RouteSpec type:
    *   Rename the response classes field from `Responses` to `ResponseClasses`.
    *   Change the JSON and YAML serialization key from 'responses' to 'response_classes'.
*   Update the ResponseClass type:
    *   Rename the success/failure indicator field from `IsSuccess` to `IsFailure`.
    *   Change the JSON and YAML serialization key from 'is_success' or 'isSuccess' to 'is_failure'.
    *   Ensure `IsFailure: true` replaces the former `IsSuccess: false`.
*   Modify code to:
    *   Use `route.ResponseClasses` instead of `route.Responses`.
    *   Use `rc.IsFailure` directly instead of `rc.IsSuccess`.
*   Identify ServiceProfile resources by the fully-qualified DNS name format: '{service}.{namespace}.svc.cluster.local'.
*   Ensure profile conversion:
    *   Combines multiple request match conditions into an ALL match sequence.
    *   Combines multiple response match conditions into an ALL match sequence.
*   Implement `buildConfig` function in `cli/cmd/profile.go`:
    *   Accepts `namespace` and `name` strings.
    *   Returns a configuration object for `renderProfileTemplate`.
*   Implement `renderProfileTemplate` function in `cli/cmd/profile.go`:
    *   Accepts the config from `buildConfig` and an `io.Writer`.
    *   Renders a valid YAML ServiceProfile to the writer.
    *   Returns `nil` on success or an error on failure.
*   Ensure the rendered profile template:
    *   Has `APIVersion` 'linkerd.io/v1alpha1', `Kind` 'ServiceProfile'.
    *   Uses `Name` '{serviceName}.{namespace}.svc.cluster.local'.
    *   Sets `Namespace` to the control plane namespace.
    *   Includes an example route with:
        *   `Name` '/authors/{id}', `Condition` Path '/authors/\d+' and Method 'POST'.
        *   A `ResponseClass` with Status range Min=500 Max=599 and `IsFailure=true`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.