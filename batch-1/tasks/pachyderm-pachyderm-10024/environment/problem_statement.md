## Description

An intermediate database migration state representing the schema at version 2.8.0 is currently defined as package-private (unexported), which means only code within the same package can reference it. This prevents external packages — including test infrastructure — from using this specific migration checkpoint as a starting point or reference state.

## Expected Behavior

- The version 2.8.0 migration state should be accessible from outside its defining package, so that other packages can use it as an intermediate migration target.
- Test code and other packages that need to run the system with migrations applied only up to version 2.8.0 (rather than the full chain) should be able to reference this state directly.
- All existing internal usages within the package should continue to work correctly after the visibility change.

## Why This Matters

Being able to reference specific intermediate migration states from external packages is essential for testing new migration steps in isolation. Without this, there is no way to start the system at a known prior version state and then test a specific new migration step on top of it. Making the 2.8.0 state publicly accessible unblocks this testing pattern and enables more targeted, incremental migration tests.
