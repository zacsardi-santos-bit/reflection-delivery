## Description

Workspace projects that use a shared catalog to pin dependency versions cannot currently reference those catalog entries from within override declarations. This means developers must duplicate version strings between the catalog and the overrides section, leading to drift when the catalog version is updated but the override is not.

## Expected Behavior

- When an override entry uses the catalog reference syntax (with no suffix), the system should resolve the override to the version pinned in the default workspace catalog under the package's name.
- When an override entry specifies a named catalog (with a non-empty suffix), the system should resolve the override from that named catalog.
- If an override key includes a version selector, the catalog lookup should use only the package name portion of the key.
- Catalog references should also work in nested override declarations.
- If the referenced package is not found in the specified catalog, a clear error should be returned identifying both the override key and the catalog that was searched.

## Why This Matters

Without this feature, teams maintaining monorepos with shared version catalogs are forced to maintain version numbers in two places. With this change, an override can simply point to a catalog entry, keeping the configuration DRY and eliminating the risk of version mismatches between the catalog and overrides.
