## Description

The Java client code generator does not produce model classes or API handler code for inline schemas defined inside webhook definitions in OpenAPI 3.1 specifications. When an API spec includes a webhook with an inline request body schema, the generator silently skips that schema during model resolution, leaving the generated client missing the corresponding classes. As a result, any code that references those generated types fails to compile.

This is inconsistent with how the generator handles inline schemas in regular API paths — for those, it correctly extracts and names the schemas and generates corresponding model classes and API code.

## Expected Behavior

- When an OpenAPI 3.1 spec defines webhooks with inline request body schemas, the generator should process those schemas the same way it processes inline schemas in regular paths.
- The generated client should include model classes for each inline schema found in a webhook definition.
- The generated client should include an API handler class for the webhook operations.
- For a webhook POST operation with an inline request body schema containing a timestamp field, an event type field, and a nested event subobject (which itself has an identifier field), the generator should produce:
  - A model class for the request payload
  - A nested model class for the event object
  - An API class with a method for that webhook endpoint

## Why This Matters

Developers using OpenAPI 3.1 specs that define webhooks end up with broken generated clients because the inline schemas in webhook definitions are not resolved. This forces them to write manual workarounds or use external schema references instead of inline schemas, which is contrary to the flexibility the specification is supposed to provide.
