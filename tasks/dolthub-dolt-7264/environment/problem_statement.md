## Description

Dolt's SQL server YAML configuration has grown to include many options over time. When older versions of a Dolt server or client receive configuration that includes fields they were never built to handle, it can cause unexpected failures. We need a way to annotate each configuration field with the minimum server version in which it was introduced, so that configuration can be safely serialized for older versions by automatically removing fields they don't support.

Additionally, without any enforcement mechanism, developers can accidentally add new configuration fields without marking them with a proper version — or mark them as "to be determined" and never update them before release. We need a validation system that catches these cases automatically during testing.

## Expected Behavior

- Configuration fields should support a minimum-version annotation indicating when they were introduced
- A utility should be able to take a version number and a configuration object and produce a version-safe copy with unsupported fields removed
- Fields annotated as "not yet versioned" (pending release) should also be stripped during serialization
- Fields that carry a version annotation must be nullable types (so they can be set to absent) and must be marked as optional in the YAML output
- A validation file should track the version metadata of all configuration fields, and tests should fail if new fields are added without proper version annotations or if the validation file is out of date

## Why This Matters

This allows the server to safely share configuration with older cluster members or replication targets that wouldn't understand newer config options, preventing startup failures and unexpected behavior when mixed-version deployments are in use.
