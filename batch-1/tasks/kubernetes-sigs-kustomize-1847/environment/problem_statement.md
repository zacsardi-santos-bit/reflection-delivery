## Description

The filesystem abstraction package in kustomize is missing a couple of shared utility primitives that are commonly needed when working with file paths. There is currently no exported, centralized way to strip trailing path separators from a path string, nor is there an exported constant representing the current directory notation (".").

## Expected Behavior

- A utility function should be added to the filesys package that accepts a path string and returns it with any trailing path separators removed. If the path has no trailing separators it should be returned as-is. If the path is entirely separators, an empty string should be returned.
- An exported constant representing the current directory (a single dot) should be available from the package so callers throughout the codebase can reference it without redefining it.

## Why This Matters

Without these shared utilities, callers have to reimplement path-stripping logic themselves or cannot share a single authoritative definition of common path tokens. Centralizing these in the filesys package makes path manipulation consistent and reusable across the kustomize codebase. It also eliminates duplicate ad-hoc implementations scattered across callers.
