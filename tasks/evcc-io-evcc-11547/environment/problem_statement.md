## Description

The project currently depends on an older mock generation tool for Go testing that has since been archived and is no longer actively maintained. The community has migrated to a fork of that tool that is now the actively maintained successor. We need to update the entire codebase to use this successor library so that we're not depending on unmaintained tooling.

## Expected Behavior

- All test files should import the mock framework from the new, maintained library location rather than the archived one.
- Auto-generated mock files across the codebase should be regenerated using the new tool.
- The mock for the e-mobility interface used by one specific charger type should be generated locally within that charger's own module (not imported from an external module), so that those charger tests are self-contained and don't depend on the external module's mock.
- The project's dependency manifest should reflect the new library as a direct dependency.

## Why This Matters

Depending on archived, unmaintained libraries creates technical debt and blocks future updates to the Go ecosystem. Migrating to the actively maintained successor ensures that the mock tooling stays current, benefits from ongoing fixes and improvements, and keeps the project in good standing with modern Go development practices.
