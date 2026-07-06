## Description

The CLI tool's template directory copying utility depends on a directory-reading library. This library released a major version update that changed its public API in a breaking way: previously, the promise-based listing function was accessed as a method on the library's default export, but in the new version that method no longer exists — instead, the functionality is exposed as a named export.

After upgrading this library to its latest version, the test suite started failing. The specific test that verifies template files are correctly written to disk began throwing errors because both the internal implementation and the test code were still using the old API pattern.

## Expected Behavior

- When the library is at its latest major version, the template directory copying utility should continue to work correctly.
- Importing the library should use named exports (as exposed by the new version) rather than the old default export pattern.
- All template files should be copied to the output directory without errors.

## Why This Matters

The template directory copying feature is used when creating new Netlify CLI projects and functions from templates. If it's broken, users cannot scaffold new projects from templates. Keeping dependencies up to date while adapting to API changes is essential for the CLI's correctness and security.