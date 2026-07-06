## Description

The project currently has integration and end-to-end tests scattered across multiple separate build modules. There is one module for general integration testing, a separate module specifically for testing modern language features, another for testing compatibility with a popular annotation-processing library, and yet another for database-specific tests. This fragmented structure makes it difficult to understand the scope of end-to-end testing at a glance, forces contributors to run tests in multiple modules, and adds maintenance overhead from managing several small modules.

## Expected Behavior

- All integration and end-to-end tests should be consolidated into a single unified module (named "e2e").
- The new module should contain tests covering general integration scenarios, modern language features, annotation processor compatibility, and cross-cutting concerns.
- The new module must be independently buildable and testable with a single Maven command targeting only that module.
- The module must correctly declare all necessary dependencies so every category of test can compile and run, including tests that use SQL templating, annotation processing, and modern language features.
- The new module must be configured to use a minimum Java version sufficient to support tests that rely on modern language features introduced in a recent Java release.

## Why This Matters

Consolidating all end-to-end and integration tests into one module simplifies the project structure, reduces the number of top-level modules contributors need to be aware of, and makes it easy to run the full integration test suite with a single command.
