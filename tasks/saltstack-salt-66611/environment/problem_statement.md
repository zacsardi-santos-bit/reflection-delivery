## Description

Several macOS package management operations are missing the ability to pass extra flags to the underlying package manager. This is a problem when the same package name exists as both a command-line formula and a desktop application (cask), because without specifying which type you want, the operation may act on the wrong variant or fail entirely.

Specifically, the following operations do not currently accept additional flags:

- Querying the latest available version of a package
- Removing/uninstalling a package
- Retrieving detailed information about an installed package
- Listing packages with available upgrades

## Expected Behavior

- Each of the above operations should accept an optional list of extra flags that get forwarded to the package manager in the appropriate position in the command.
- When extra flags are passed, they should appear between the fixed base flags and the package names in the generated command.
- The version query operation should also correctly handle both formula-type and cask-type packages, returning the version from whichever format the package uses.
- When a formula has published aliases, listing installed packages should also include entries for those aliases, so the package is discoverable by any of its known names.

## Why This Matters

Users who have packages installed from custom taps — or who have packages that exist in both formula and cask forms — currently cannot perform common operations on them unambiguously. Adding an options passthrough resolves the ambiguity and makes the module usable for a wider range of real-world Homebrew setups.
