## Description

There are three related correctness issues in the package manager that need to be fixed:

**1. Prototype pollution when storing specially-named dependencies**

When a project has dependencies whose names happen to match reserved JavaScript object property names, updating the project manifest silently corrupts global JavaScript state instead of safely recording those packages. This means that installing a package with one of these unusual-but-valid names can have unexpected global side effects. The manifest update logic should store all dependency entries safely, regardless of their name.

**2. Unhelpful error message when a version number is accidentally included in the package name**

A common user mistake is to accidentally include a version number as part of the package name (for example, when copying from documentation). When this happens, the registry returns a "not found" error because the full string including the version isn't a real package name. The package manager should detect this pattern and suggest the correct package name in the error message. This should work for both regular and scoped (namespaced) packages, should only appear on genuine "not found" errors, and should not produce a suggestion when no meaningful name can be recovered. Additionally, the current implementation of this detection has a performance issue — certain inputs can make it take an extremely long time to process.

**3. Incomplete URL encoding for scoped package names in provenance workflows**

When checking package visibility during a provenance-enabled publish, scoped package names (which contain a separator character in their name) are only partially encoded in the registry URL. Only the first separator is encoded, leaving subsequent ones unencoded. All separator characters should be encoded.

## Expected Behavior

- Updating a manifest with a dependency whose name matches a JavaScript built-in property name stores it correctly as a regular dependency entry without modifying global state.
- When a 404 error occurs and the requested name appears to include an appended version, the error message suggests the name without the version.
- The suggestion is not shown for non-404 errors, for names with no version suffix, or when stripping the version would leave an empty name.
- The version-detection logic performs efficiently on any input.
- All separator characters in scoped package names are encoded when constructing registry API URLs.

## Why This Matters

The prototype pollution issue is a security and correctness concern. The error hint helps users identify and fix a common typing mistake quickly. The URL encoding fix ensures that package visibility checks work correctly for any valid package name.
