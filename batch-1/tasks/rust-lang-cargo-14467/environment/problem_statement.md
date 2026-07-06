## Description

The experimental feature that allows package names to include namespace separators (double-colon segments) is partially broken: while such packages can be declared and built, several commands that accept package specifications do not recognize namespaced names as valid targets.

Specifically, when a package has a namespaced name, the package update command fails to accept the namespaced name as a valid specification — whether it is provided as a short name or as a fully qualified package identifier. This makes it impossible for users of namespaced packages to selectively update those packages by name.

## Expected Behavior

- Running the update command and specifying only the namespaced package name (e.g. the name containing the double-colon separator) should succeed, updating that package without error.
- Running the update command and specifying the full qualified package identifier (which includes the namespaced name in its path fragment) should also succeed without error.
- In both cases, when no dependency update is required, the command should report that zero packages were locked to newer versions.
- The command for displaying a package's identifier should correctly output the full package ID including the namespaced name, in the standard format used for package ID specs.

## Root Cause

The package ID specification parser treats the colon character as a separator between the package name and version. When a package name contains double-colon namespace separators, the parser incorrectly splits on the first colon, resulting in an invalid parse of the name and version components.

## Why This Matters

Developers adopting namespaced package names should be able to use all standard Cargo commands with those packages. Failing to parse namespaced names in package specification arguments makes common workflows like selective dependency updates impossible for namespaced packages.
