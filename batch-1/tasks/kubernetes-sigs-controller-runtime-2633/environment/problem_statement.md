## Description

The fake client used for testing Kubernetes controllers has two related issues with how it handles object metadata.

**Issue 1: Type metadata is incorrectly populated after Get/List**

When using the real Kubernetes API with typed Go objects (concrete structs rather than unstructured types), the API server does not populate the API version and kind fields on returned objects — that information is already encoded in the Go type itself. However, the fake client populates those fields after every Get or List operation. This inconsistency means tests written against the fake client must work around the difference, adding extra setup steps that would not be needed with a real cluster.

**Issue 2: Pointer-embedded metadata causes failures**

Some custom resource types embed their metadata fields as pointer fields rather than as value fields. This is a valid Go struct pattern, but the fake client does not handle it — attempting to use Get, List, Patch, Update, or Delete with such types results in failures. This makes it impossible to write controller tests for any resource type that uses this embedding style.

## Expected Behavior

- After a Get or List using typed objects, the type metadata fields (API version and kind) on the returned objects should remain empty, matching the behavior of real clients.
- The fake client should handle all standard operations — Get, List, Patch, Update, and Delete — correctly for types that embed metadata via pointer fields.

## Why This Matters

Tests that rely on the fake client should behave consistently with tests against real clusters. Custom resource authors using pointer-style metadata embedding should be able to use the fake client without hitting crashes or unexpected errors.
