## Description

When Playwright end-to-end projects are set up in an Nx workspace, the generated configuration may not include browser project definitions — the section that specifies which browsers (such as Chromium, Firefox, and WebKit) the tests should run against. Without this section, cross-browser test runs are incomplete or may not work as intended.

A migration is needed to automatically detect these incomplete Playwright configurations across all projects in the workspace and fill in the missing browser project definitions. If a configuration already has browser project definitions, it should remain completely untouched.

## Expected Behavior

- The migration inspects all projects in the workspace for a Playwright configuration file.
- For any configuration file that is missing browser project definitions, the migration adds default entries for Chromium, Firefox, and WebKit, using the corresponding device descriptors.
- The migration also ensures that the necessary browser device identifiers are properly imported at the top of the configuration file.
- Configuration files that already have browser project definitions are left unchanged.

## Why This Matters

Users who created Playwright e2e setups before this improvement was made will have configuration files that lack browser targeting. Rather than requiring each developer to manually update every config file, this migration handles the update automatically when upgrading, making the transition seamless.
