## Description

When policies or cleanup policies are submitted with names that exceed the 63-byte limit, the validation system returns an error message to describe the constraint. The current wording of this error message is out of date and inconsistent with the standardized phrasing used in the broader Kubernetes ecosystem for expressing field length constraints.

## Expected Behavior

- When a policy name (for any policy type) is too long, the validation error detail should use the standardized Kubernetes phrasing for length constraint violations, consistent with what the upstream Kubernetes validation library now uses
- The full formatted error string for top-level policy resources should combine the name field path, a "Too long" prefix, and the standardized detail phrase
- The full formatted error string for cleanup policy resources should follow the same format using the metadata name field path instead
- This consistent wording should apply across all API versions (v1, v2, v2beta1) and all policy types (Policy, ClusterPolicy, CleanupPolicy, ClusterCleanupPolicy)

## Why This Matters

The previous wording does not match the standardized language now used by the Kubernetes validation libraries for the same type of constraint. Keeping these messages aligned with upstream conventions ensures that operators and tooling that parse or display validation errors see consistent, expected language rather than outdated phrasing.
