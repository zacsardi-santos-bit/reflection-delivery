## Description

A new test file was added to the metadata package that uses an external property-based testing library. However, the project's module dependency declarations were not updated to include this library, causing the entire package to fail to build.

Because Go requires all imported packages to be declared as module dependencies, the missing entry breaks compilation for the whole package — not just the new test file. This means none of the existing tests in the metadata package can run at all, even though they have nothing to do with the new test.

## Expected Behavior

- The project's module dependency file should list the property-based testing library as a required dependency at the appropriate version
- The module checksum file should include the corresponding verification entries for that library
- All existing tests in the metadata package should compile and pass once the dependency is properly declared

## Why This Matters

Without this fix, any developer running the metadata package's test suite gets a compilation failure and cannot verify anything in that package. Adding the missing dependency declaration restores the ability to build and test the package normally.
