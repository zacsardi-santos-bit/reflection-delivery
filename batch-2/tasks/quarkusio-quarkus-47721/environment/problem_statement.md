## Description

When using Quarkus's reactive REST layer with Jackson for JSON serialization, classes that define JSON property names exclusively through constructor parameter annotations do not serialize correctly. Such classes — common when Kotlin data classes are compiled to JVM bytecode — rely on constructor annotations to map snake_case JSON field names to camelCase Java field names. While deserialization (reading incoming JSON) works correctly, the serialization side (writing outgoing JSON) ignores the constructor annotation names and falls back to the camelCase names derived from getter methods.

This means a REST endpoint that accepts and returns such a class will correctly receive snake_case field names from the request body, but will respond with camelCase field names derived from getter methods instead of the original snake_case names. This breaks API contracts for clients expecting consistent naming in responses.

## Expected Behavior

- Classes using only constructor parameter annotations to define JSON property names should be serialized using those same property names
- A REST endpoint that echoes such a class should return a JSON response with the same snake_case field names that were received in the request
- The JSON round-trip (deserialize request, then serialize response) should be symmetric: the same property names used in the incoming request body should appear in the outgoing response body

## Why This Matters

Kotlin data classes are a common pattern in JVM applications and produce bytecode where all property annotations appear on constructor parameters. Quarkus applications mixing Kotlin and Java code, or Java code that mimics this pattern, are affected. Without this fix, developers must add redundant annotations to every getter method to work around the asymmetric serialization behavior.
