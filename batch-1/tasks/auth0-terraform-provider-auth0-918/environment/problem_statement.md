## Description

The Auth0 Terraform provider currently has no way to manage the custom HTML content that can be injected into specific sections of Universal Login prompt screens. Teams who want to add custom markup to areas such as the start or end of the login form, the footer, or the secondary actions section have to manage these customizations outside of Terraform, which breaks the infrastructure-as-code workflow.

## Expected Behavior

- A new managed resource should allow operators to declaratively specify custom HTML for each injectable section of a given prompt (such as the login or signup screen).
- The resource should support a full lifecycle: creating the customization, reading it back, updating individual sections, and removing all customizations when the resource is destroyed.
- When only some sections are configured, unset sections should be treated as empty.
- Updating the configuration should reflect the new values correctly when the state is read back.

## Why This Matters

Without this capability, teams cannot use Terraform to manage the full appearance of their Universal Login experience. Any customizations to prompt partials would need to be applied and updated manually or through a separate toolchain, undermining consistent and auditable infrastructure management.
