## Description

Deserializing package metadata from the npm registry fails for many real-world packages (including several widely-used ones) because the registry sometimes includes a deprecated field with a boolean value rather than a string.

The type definition for the deprecated field in package version manifests declares it as a string (the deprecation reason), and the JavaScript implementation handles the boolean case silently via truthiness checks. However, the Rust deserialization code is strict about types, and when it encounters a boolean false value for the deprecated field, it fails to parse the entire packument.

## Expected Behavior

- When the deprecated field is missing or null in the JSON, it should be treated as "not deprecated."
- When the deprecated field is a boolean false, it should also be treated as "not deprecated" (same as absent).
- When the deprecated field is a boolean true, it should be treated as deprecated with an empty reason string.
- When the deprecated field is a string, it should be treated as deprecated with that string as the reason.

This normalization should happen transparently during deserialization, so callers always receive a consistent optional string shape.

## Why This Matters

Without this fix, any install that involves packages which have even one version with a boolean false deprecated field in the registry metadata will fail entirely. This blocks the integrated benchmark workload and makes the package manager unusable for large dependency trees.
