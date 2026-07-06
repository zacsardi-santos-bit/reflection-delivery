## Description

When introspecting a chat prompt template's input schema, the schema does not include a required field listing which input variables are mandatory. This means that even variables explicitly declared as required (i.e. not marked optional) do not appear in the required list — instead, all inputs look equally optional from the schema's perspective.

This makes it impossible for downstream tooling, documentation generators, or validation systems to correctly determine which inputs must be provided before invoking the template. For example, a message history placeholder declared as non-optional should cause a validation error if it is omitted, but the schema does not communicate this constraint.

## Expected Behavior

- The input schema for a chat prompt template should include a required field that lists all non-optional input variable names.
- A message placeholder marked as required should appear in the required list; one marked as optional should not.
- Attempting to validate inputs against the schema without providing a required field should raise a validation error.
- Regular string template variables (named placeholders declared in message templates) should always be listed as required, since they are always mandatory inputs with no optional flag.

## Why This Matters

Any tool that uses the input schema to understand a prompt template's interface — for example, to generate API documentation, validate user-provided inputs, or auto-fill defaults — will be incorrect if the schema omits required field information. Fixing this brings the schema into alignment with the template's actual runtime validation behavior.
