## Description

The operator needs to be updated to work with the newer version of the Kubernetes controller framework library. The newer version introduced several breaking API changes that must be addressed throughout the codebase.

## Expected Behavior

- Event handler callback methods (for create, update, delete, and generic events) must accept a context object as their first argument. Any handler type that implements the event handler interface must be updated accordingly.
- Two utility functions for constructing resource cache builders should be extracted into the shared controller package so they can be reused across the operator:
  - One for building a cache scoped to a specific list of namespaces
  - One for building a cache filtered to resources matching a given label selector, for a particular resource type
- Existing code that constructs these cache builders inline should be replaced with calls to the new shared functions.
- Unit tests that use a fake Kubernetes client must explicitly declare which resource types have status subresources; without this, status updates are silently dropped in the newer framework version.
- Tests that set a deletion timestamp on a resource must do so after creating the resource in the fake client, since the fake client resets any deletion timestamp set before creation.

## Why This Matters

Without these updates, the operator fails to compile and all tests fail against the updated controller framework. The cache builder extraction also reduces duplication and makes it easier to apply consistent cache configurations in both the production binary and end-to-end test helpers.
