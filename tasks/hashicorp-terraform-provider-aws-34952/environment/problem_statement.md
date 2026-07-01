## Description

The AWS Terraform provider should expose built-in functions that allow users to construct and deconstruct AWS resource identifiers directly within their Terraform configurations. Currently, users who need to build an identifier from individual components (such as partition, service, region, account ID, and resource path) or decompose an existing identifier into its parts must rely on string manipulation workarounds or external data sources.

## Expected Behavior

- A new provider-scoped function should accept the individual components of an AWS resource identifier and return the fully assembled identifier string. For example, providing the partition, service, an empty region, an account ID, and a resource path should produce the correct, colon-delimited identifier.
- A complementary provider-scoped function should accept a complete AWS resource identifier string and return an object containing each component as a separate attribute.
- When the parsing function receives a string that does not conform to the expected identifier format, it should fail with a clear error indicating that the input is not a valid identifier (specifically, that the prefix is invalid).

## Why This Matters

These functions make it significantly easier to work with AWS resource identifiers in Terraform configurations without resorting to fragile string interpolation. They also provide proper validation — invalid inputs produce descriptive errors rather than silently incorrect output.
