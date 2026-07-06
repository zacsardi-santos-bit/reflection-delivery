## Description

When a dataset or chart contains a SQL template with invalid syntax — for example, using a natural language phrase inside a template expression instead of the correct syntax — the system currently returns a generic HTTP 400 Bad Request error. This response doesn't accurately reflect the nature of the problem and makes it harder for callers to handle errors programmatically.

Additionally, there is no standalone utility to validate template syntax upfront, before templates are executed. This means invalid templates are only discovered at execution time, and the error surfaces through a generic path rather than a clearly labeled template validation failure.

## Expected Behavior

- When a template contains user-caused syntax errors (invalid syntax, undefined variable references, or security policy violations), the system should return HTTP 422 Unprocessable Entity. The response should include a descriptive message identifying the type of error and the problematic template content.
- When template processing fails due to a server-side problem (such as resource exhaustion), the system should return an appropriate server error (HTTP 500), clearly distinguished from user-input errors.
- A new utility module should be available that can validate Jinja2 template syntax independently, for both raw template strings and for JSON structures that may contain embedded templates in filter expressions. Invalid templates should produce clearly labeled validation errors, and passing a null value or empty string should be treated as valid input.

## Why This Matters

Returning the wrong HTTP status code for template errors makes it difficult for API consumers to distinguish client-side mistakes from server failures. Providing upfront template validation allows systems to reject bad input early, with actionable error messages, before attempting execution.
