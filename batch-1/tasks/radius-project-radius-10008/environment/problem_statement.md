## Description

When users register a custom (user-defined) resource type in Radius and define a JSON schema for it, that schema is stored but never actually enforced at deployment time. This means a user can deploy a resource with missing required fields or values of the wrong type, and the platform happily accepts it without any validation error. The schema exists but provides no guarantees.

## Expected Behavior

- When a user deploys a dynamic resource type, Radius should validate the submitted resource properties against the JSON schema registered for that resource type and API version.
- If the properties violate the schema — for example, a required field is absent or a field has the wrong data type — the deployment should fail with a clear, descriptive error indicating what went wrong.
- If no schema is registered for the resource type, deployment should proceed without validation (graceful degradation).
- Validation should only apply to create/update operations, not to delete operations.

## Why This Matters

Schema definitions for user-defined resource types are currently documentation-only. Developers expect that registering a schema for their resource type will enforce structure on deployed resources. Without enforcement, invalid resources can be created silently, leading to hard-to-debug runtime failures downstream. Making schema validation enforceable at deployment time closes this gap and improves reliability for operators building on Radius's extensibility features.
