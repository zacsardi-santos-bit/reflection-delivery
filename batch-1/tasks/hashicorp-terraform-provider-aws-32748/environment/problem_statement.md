## Description

The CloudFormation stack resource in the Terraform AWS provider is missing a consistent, well-integrated function for looking up a stack by name. Currently, stack lookup is done in multiple places using raw API calls, which doesn't integrate with the provider's standard "not found" handling patterns. This means that when a stack is deleted outside of Terraform, read and destroy operations may error instead of gracefully removing the resource from state.

## Expected Behavior

- There should be a single, exported stack lookup function that accepts a stack name and returns the stack details.
- When a stack does not exist (or has been fully deleted), the lookup function should signal a "not found" condition using the provider's standard not-found error mechanism, so callers can remove the resource from state cleanly.
- The old stack lookup function (which was used before this change) should be removed and replaced with the new one across the CloudFormation service package and any other packages that depend on it.

## Why This Matters

Without this change, users who delete a CloudFormation stack outside of Terraform may encounter errors during plan/apply instead of the expected behavior of Terraform detecting the stack is gone and offering to recreate it. Consolidating the stack lookup logic also reduces code duplication and makes the error handling behavior more predictable and consistent with the rest of the provider.
