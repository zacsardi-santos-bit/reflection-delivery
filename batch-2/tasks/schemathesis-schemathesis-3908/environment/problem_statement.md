## Description

The error feedback system can parse validation error responses from Java/Spring and Pydantic-based APIs to refine test generation, but there is no equivalent support for APIs built with the popular Python REST framework for Django. When such an API returns a validation failure, the tool cannot extract any useful signal from the response, so test cases are never refined based on those errors.

Additionally, the data structure that carries type mismatch information is labeled with a Java-specific term. This naming is confusing and incorrect when the same structure needs to represent errors from non-Java backends, making the codebase harder to extend.

## Expected Behavior

- A new parser should be added that recognizes and extracts observations from the error format used by Django-based REST APIs. It should handle:
  - Flat and nested field error dictionaries
  - List-of-dicts (serializer list errors with integer-indexed positions)
  - Required/blank/null field messages
  - Format errors (email, URL, UUID, date, datetime, time)
  - Type mismatch messages asserting a primitive type name
  - String and array size constraints
  - Numeric bounds (minimum, maximum, inclusive and exclusive)
  - Object-level (non-field) errors should be silently ignored since they cannot be attributed to a specific field

- The new parser must be registered so it is automatically used alongside the existing parsers.

- The type mismatch data structure's field should be renamed to a language-neutral name that works for both Java fully-qualified class names and simple JSON Schema type tokens.

- The schema adjustment logic should be extended so that when a simple primitive type name is reported, the schema's type constraint is updated directly rather than being silently ignored.

- For GET/DELETE/HEAD requests, field errors should be attributed to query parameters rather than the request body.

## Why This Matters

Developers testing Django REST framework APIs currently get no benefit from the error feedback mechanism. Supporting the DRF error format means the tool can automatically tighten generated test inputs based on what the server rejects, leading to better test coverage without manual schema annotation.
