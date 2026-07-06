Unify the naming scheme for API schema specifications across the AI Gateway configuration. Ensure that all schema fields, whether for client-side or backend configurations, use consistent field names. Implement these changes in the Go structs, YAML configuration, Kubernetes custom resource definitions, and validation error messages.

*   Update the filterconfig package:
    *   Rename the `VersionedAPISchema` struct's schema-identifier field from `Schema` to `Name`.
    *   Rename the `Config` struct's top-level API schema field from `InputSchema` to `Schema`.
        *   Change the YAML key from `inputSchema` to `schema`.
    *   Rename the `Backend` struct's backend API schema field from `OutputSchema` to `Schema`.
        *   Change the YAML key from `outputSchema` to `schema`.
    *   Change the YAML serialization of `VersionedAPISchema` to use `name` instead of `schema`.

*   Update the aigv1a1 package:
    *   Rename the `VersionedAPISchema` struct's schema-identifier field from `Schema` to `Name`.

*   Modify Kubernetes custom resource definitions:
    *   For `AIGatewayRoute`:
        *   Rename `spec.inputSchema` to `spec.schema`.
        *   Within `spec.schema`, rename the sub-field from `schema` to `name`.
        *   Update CEL validation error messages to reference `spec.schema` and `spec.schema.name`.
            *   Non-OpenAI schema error: `spec.schema: Invalid value: "object": failed rule: self.name == 'OpenAI'`.
            *   Unknown schema error: `spec.schema.name: Unsupported value: "SomeRandomVendor": supported values: "OpenAI", "AWSBedrock"`.
    *   For `AIServiceBackend`:
        *   Rename `spec.outputSchema` to `spec.schema`.
        *   Within `spec.schema`, rename the sub-field from `schema` to `name`.
        *   Update CEL validation error messages to reference `spec.schema.name`.
            *   Unknown schema error: `spec.schema.name: Unsupported value: "SomeRandomVendor": supported values: "OpenAI", "AWSBedrock"`.

*   Ensure the router's backend selection returns a `Backend` struct with the schema field named `Schema`, and verify that the returned `Backend.Schema` matches the configured schema.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.