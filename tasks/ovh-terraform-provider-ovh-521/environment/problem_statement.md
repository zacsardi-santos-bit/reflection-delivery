## Description

The Terraform provider currently supports managing IAM policies but has no support for IAM permissions groups — reusable, named collections of allowed, excepted, and denied cloud API actions. Without this, teams are forced to duplicate action lists across every individual policy, making large permission configurations hard to maintain and impossible to share.

## Expected Behavior

- Users should be able to declare a permissions group as a Terraform-managed resource, specifying a name, description, and up to three sets of actions: those explicitly allowed, those excluded from the allow set, and those always denied.
- The resource should expose its unique identifier (URN) so it can be referenced elsewhere in Terraform configurations.
- The resource should support import by its URN so existing groups can be brought under Terraform management.
- A data source should allow looking up a specific permissions group by its URN, returning all of its attributes.
- A separate data source should allow listing the URNs of all available permissions groups, enabling dynamic references within a configuration.

## Why This Matters

Permissions groups are a key building block for composable, maintainable IAM configurations. Supporting them in Terraform enables teams to define a permission set once and reuse it across multiple policies, reducing duplication and configuration drift.
