## Description

The release workflow's package-selection dropdown currently lists entries as full directory paths (e.g. with path prefixes included). This is verbose and makes it harder to maintain — every new package requires careful manual construction of the full path. The dropdown would be cleaner and less error-prone if it used short, human-readable package names instead.

However, switching to short names breaks a validation script that checks whether every package directory on disk has a corresponding dropdown entry. That script currently compares dropdown values directly against directory paths, so it would no longer find any matches once the dropdown switches to short names.

## Expected Behavior

- The dropdown in the release workflow should use short names (just the package identifier, no path prefix).
- The validation script must correctly reconstruct full directory paths from short names before comparing against actual packages on disk.
- Top-level packages (core, base framework packages, and test utilities) resolve to a different path prefix than partner integration packages.
- After the change, the validation script should confirm that every package directory on disk is represented in the dropdown, and every dropdown entry corresponds to a real directory.

## Why This Matters

Having full paths in the dropdown is fragile and verbose. Short names are easier to read and less likely to be mistyped. The validation script keeps the dropdown in sync with the actual repository structure, so it must understand the path expansion rules to remain useful after this change.
