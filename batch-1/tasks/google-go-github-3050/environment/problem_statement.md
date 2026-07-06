## Description

GitHub deployment environments support custom protection rules — third-party app integrations that can review and gate a deployment before it proceeds. These rules let teams enforce compliance checks, security scans, or other approval workflows as part of their deployment process.

Currently, the Go client library has no support for managing custom deployment protection rules. Developers who want to list, enable, retrieve, or disable these rules for a repository environment have to resort to making raw HTTP calls rather than using the idiomatic Go client API.

## Expected Behavior

The client library should support the following operations for custom deployment protection rules on a given repository environment:

- Retrieve all currently enabled custom protection rules for an environment
- List the available integrations (apps) that can be enabled as protection rules for an environment
- Enable (create) a new custom protection rule using a specific app integration
- Retrieve the details of a single custom protection rule by its ID
- Disable (remove) a custom protection rule from an environment

## Why This Matters

Without these methods, Go-based automation tools cannot programmatically manage deployment protection rules at all. Adding this support brings the Go client to parity with the GitHub REST API for this feature area, enabling teams to automate deployment governance workflows in Go.
