## Description

The semantic kernel Python library's data and exception classes are scattered across deep internal sub-module paths, forcing developers to reference internal implementation details when importing them. For example, to use a vector search result class, you need to know the exact internal file it lives in. This is fragile — any internal refactoring breaks consumer imports — and is inconsistent with how Python packages typically expose their public APIs.

## Expected Behavior

- All data-related types, mixins, constants, and utilities should be importable directly from the top-level data package without needing to reference internal sub-modules.
- All exception classes related to data and memory operations should be importable from the top-level exceptions package, including a new dedicated exception class for vector store text search validation errors.
- AI connector types (such as the Azure text embedding service) should be importable from their connector's top-level package, not from deep internal paths.
- Utility functions and base classes should also be accessible at the appropriate top-level package level.
- When creating a vector store text search instance with invalid arguments (missing embedder or no collections), a specific, dedicated exception type should be raised rather than a generic Python error — allowing callers to programmatically distinguish these validation failures.

## Why This Matters

Exposing a clean, stable public API at the package level allows developers to write more maintainable code that doesn't break when internal module structure changes. It also makes the library easier to discover and use. Introducing a dedicated validation error type (instead of generic errors) gives callers the ability to handle these cases explicitly.
