## Description

When generating TypeScript client code from an OpenAPI specification, the tool currently discards any description and summary text that is associated with individual response definitions. As a result, the generated TypeScript types contain no documentation comments, even when the original API spec has rich descriptive text for each response.

This is a problem because developers using the generated client code rely on IDE hover documentation to understand what different response types mean. When those comments are absent, they have to refer back to the original API spec manually.

## Expected Behavior

- When a response definition in the OpenAPI spec has both a summary and a description, the generated TypeScript response type should be preceded by a documentation comment block that contains the summary on the first line, a blank separator line, and then the description.
- When a response definition has only a description (no summary), the generated type should be preceded by a documentation comment block containing just the description.
- When a response definition has neither field, the existing behavior is preserved and no comment is generated.

## Why This Matters

Documentation from the API specification should flow through to the generated client code so that developers get useful inline context in their editors without having to consult external documentation.
