# Add Utility Functions for Package Manager Minimum-Release-Age Handling

## Description

Some modern JavaScript package managers support a "minimum release age" policy — a security feature that blocks packages released too recently from being installed. When this is configured on a project, it can silently prevent new Storybook releases from being installed, often resulting in confusing error messages with no clear guidance.

We need a set of shared utility functions that the package manager integration layer can use to detect, reason about, and respond to minimum-release-age restrictions. These utilities need to handle:

- Parsing package manager config values that represent positive integer thresholds
- Parsing and validating package release timestamps from registry responses
- Computing how old a package release is
- Finding the latest stable (non-pre-release) release that passes a given age restriction
- Determining whether a project's current exclusion list already covers Storybook packages
- Generating clear, user-facing messages that instruct users how to retry with a compatible older release

## Expected Behavior

- Config values representing valid positive integers (with optional surrounding whitespace) must be parsed and returned as a number; values representing zero, boolean false, null, undefined, or empty must be treated as unconfigured
- Package release timestamps must be parsed into Date objects, with invalid values returning null
- The function that finds a compatible older release must skip pre-release versions and return the most recent stable version that satisfies the age gate
- The exclusion-check function must support glob-style wildcard patterns, so that a wildcard pattern matching storybook-related names is recognized as covering all core Storybook packages
- Rerun instructions and commands must match the install context: creation flows and upgrade flows produce different messages
- Error log extraction must join relevant fields from structured error objects

## Why This Matters

Without these utilities, every package manager integration would need to duplicate this logic independently. Centralizing them ensures consistent behavior and makes the minimum-release-age handling maintainable across all supported package managers.
