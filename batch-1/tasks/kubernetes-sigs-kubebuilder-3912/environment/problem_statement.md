## Description

The Go v4 scaffolding plugin targets a specific version of controller-runtime when generating project scaffolds, but that version may be out of date or inconsistently referenced across the codebase. There is currently no easy, programmatic way to confirm which version is being used or to ensure it is up to date.

## Expected Behavior

- The scaffolding package should maintain a single, authoritative version identifier for the controller-runtime library it targets
- This identifier should reflect the current intended version of controller-runtime
- The identifier should be accessible within the package so that automated checks can verify it is correct and current

## Why This Matters

Keeping the controller-runtime version up to date in the scaffolding plugin ensures that newly scaffolded projects are generated with modern dependencies. A centralized identifier makes it straightforward to audit and update the targeted version in one place, and enables automated verification that the scaffolding code stays in sync with the intended library version.
