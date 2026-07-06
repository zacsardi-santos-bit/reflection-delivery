## Description

When a Terragrunt unit includes a parent configuration file that defines an exclusion block (controlling which operations like plan or apply should be skipped), and the child unit does not define its own exclusion block, the parent's exclusion settings are silently ignored after the merge. The child ends up with no exclusion configuration at all, as if the parent's block never existed.

## Expected Behavior

- If a parent config defines an exclusion block and a child includes that parent without defining its own, the child should inherit the parent's exclusion settings.
- If a child config defines its own exclusion block in addition to including a parent that has one, the child's definition should take precedence.
- This inheritance behavior should be consistent regardless of which merge strategy is used (default, shallow, or deep).
- Other top-level configuration blocks (such as retry error handling, engine settings, and feature flags) inherited through include should continue to work correctly.

## Why This Matters

Shared parent configuration files are a core pattern in Terragrunt. When teams define exclusion rules in a common root config to exclude groups of units from certain operations, those rules must flow down to child units for the pattern to be effective. The current bug makes exclusion rules defined at the parent level completely ineffective unless each child duplicates them locally — defeating the purpose of inheritance.

**Related issue:** https://github.com/gruntwork-io/terragrunt/issues/5089
