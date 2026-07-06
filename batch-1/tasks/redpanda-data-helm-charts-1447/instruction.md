Implement a dedicated Go type for service configuration in the connectors Helm chart package. Ensure that this type is exported and can be used consistently alongside other configuration types within the chart.

*   Define and export a type named `ServiceConfig` in the `charts/connectors` Go package.
    *   Ensure `ServiceConfig` is a valid Go type, such as a struct or alias.
    *   The type must be properly defined to avoid compiler errors when declared as a variable.
*   Place the `ServiceConfig` type definition in any `.go` file within the `charts/connectors` directory, such as `service.go` or `types.go`.
*   Ensure the `ServiceConfig` type is accessible and usable by chart code and tests for constructing Kubernetes Service resources.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.