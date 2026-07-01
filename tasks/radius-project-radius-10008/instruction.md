Implement schema validation for user-defined resource types in Radius to ensure that resources comply with their registered JSON schemas during deployment. Validate resources against their schemas during create and update operations, providing clear error messages when validation fails.

*   Implement `ValidateResourceAgainstSchema` in `pkg/schema/validator.go`:
    *   Accept `context.Context`, `resourceData` as `map[string]any`, and `schemaData` as `any`.
    *   Return `nil` if `schemaData` is `nil`.
    *   Return an error if `resourceData` lacks a 'properties' key, with a message containing: "resource data missing 'properties' field".
    *   Return an error if `schemaData` cannot be converted to OpenAPI schema format, with a message containing: "failed to convert schema".
    *   Return an error if resource properties fail validation, with a message containing: "resource data validation failed" and the name of the failing field.
    *   Ensure error messages for missing nested properties include: "property \"age\" is missing" (replace 'age' with the actual field name).
    *   Return `nil` if resource properties are an empty map and the schema allows an empty object.

*   Implement `GetSchemaForResourceType` in `pkg/dynamicrp/backend/processor/dynamicresource.go`:
    *   Accept `context.Context`, `*v20231001preview.ClientFactory`, `resourceID` as `string`, and `apiVersion` as `string`.
    *   Return a non-nil value assertable to `map[string]any` with a 'properties' key on success.
    *   Return an error with the invalid `resourceID` string if parsing fails.
    *   Return an error wrapping `ErrNoSchemaFound` if the API version is not found or the schema is `nil`.

*   Define `ErrNoSchemaFound` in `pkg/dynamicrp/backend/processor/dynamicresource.go`:
    *   Exported as `var ErrNoSchemaFound = errors.New("no schema found for resource type")`.

*   Implement `validateRequestSchema` method on `*DynamicResourceController` in `pkg/dynamicrp/backend/controller/dynamicresource.go`:
    *   Accept `context.Context` and `*ctrl.Request`.
    *   Return `nil` for non-PUT operations.
    *   Return an error with "invalid resource ID" if the resource ID cannot be parsed.
    *   Log a debug message and return `nil` if `ErrNoSchemaFound` is encountered.
    *   Return an error with code `v1.CodeInvalidRequestContent` and a message starting with "Schema validation failed:" if validation fails.

*   Ensure the asynchronous operation completes with status 'Failed', error code 'InvalidRequestContent', and an error message containing 'Schema validation failed' when a PUT operation violates the schema.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.