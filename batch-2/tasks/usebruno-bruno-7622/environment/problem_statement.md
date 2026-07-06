## Description

Bruno currently supports importing API collections from the newer OpenAPI 3.x format, but it has no dedicated support for the older Swagger 2.0 specification format. Many teams still maintain APIs described using Swagger 2.0, and there is currently no reliable way to import them into Bruno — the import either fails or produces incorrectly structured collections.

## Expected Behavior

- Swagger 2.0 specs (provided as either a JSON object or a YAML string) should be accepted and converted into a valid Bruno collection.
- The existing import entry point should detect Swagger 2.0 specs and route them through the new dedicated converter.
- Authentication schemes should be correctly translated: basic auth, API key (in header or query), and all OAuth 2.0 flow types (implicit, authorization code, client credentials, and password).
- Request bodies should map correctly based on the content type: JSON, XML, form-encoded, multipart form data, and plain text.
- Parameters should be fully resolved, with correct value selection (examples take priority over defaults, which take priority over enum values) and support for all array serialization formats.
- Requests should be organized into folders by tag or by URL path, with sanitized folder names.
- Operation tags on each request should have invalid characters cleaned up for compatibility.
- Response examples should be generated from response schemas and explicit example objects.
- Schemas that contain circular references should be handled gracefully without crashing the import.

## Why This Matters

Swagger 2.0 remains in wide use across many organizations and public APIs. Without proper support, Bruno users are blocked from importing a large category of real-world API specifications. A complete, robust Swagger 2.0 converter dramatically improves the usefulness of Bruno's import feature and lowers friction for teams migrating from legacy tooling.
