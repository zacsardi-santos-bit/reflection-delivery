## Description

The Neo4j GraphQL library's schema validation currently produces generic, unhelpful error messages when certain directives are used on types that are not declared as graph nodes. For example, when a relationship directive, custom resolver directive, or authorization directive is applied to a plain object type (one without the node annotation), the error message says something like "Directive X is not supported on fields of type Y." This message doesn't explain the root cause or tell the developer how to fix the issue.

## Expected Behavior

- When directives that require a type to be a graph node are used on types without that annotation, the validation error should clearly state which directive is involved and that the type needs to be annotated as a node.
- This validation must apply consistently in all usage locations: field-level usage, object-level usage, and usage within type extensions.
- Error paths must include the directive name as the final segment so developers can pinpoint the exact location of the problem.
- When these directives are used on relationship property types (which are not node types), the same clear error messages and paths should be produced.
- The error message for a default value that fails date-time type validation must clearly describe the expected type requirement rather than just saying the value is invalid.
- The error path for coalesce violations on spatial or temporal type fields should point to the directive itself, not to a nested argument.
- The error message when an authorization directive is used on a root Query field should suggest the correct alternative directive to use.
- When an authorization directive is used with no valid arguments, only a single validation error should be produced.

## Why This Matters

Clear, actionable validation messages help developers quickly identify and fix schema configuration mistakes without needing to dig through documentation. When the error clearly states which directive is misused and what annotation is required to make it valid, the developer can correct the issue immediately.
