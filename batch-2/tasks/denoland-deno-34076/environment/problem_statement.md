## Description

When a local file URL that points to a directory is fetched as an ES module, Deno produces a raw OS-level error (e.g. "is a directory" or similar filesystem error) instead of a clear, structured error message. This makes it very confusing for developers to diagnose what went wrong — the error gives no indication that the problem is an unsupported directory import.

## Expected Behavior

- Fetching a local directory URL should produce a specific, recognizable error indicating that directory imports are not supported for ES modules
- The error should carry the problematic URL so that it can be surfaced to the developer
- The error should be categorized as the appropriate JavaScript error type (a type error), not as a generic OS or I/O error
- The error message should include a well-known error code matching the Node.js convention for this scenario
- The message must not expose low-level OS error details (e.g. "os error")

## Why This Matters

Developers who accidentally write an import path pointing to a directory (e.g. forgetting the filename) currently receive a confusing, unhelpful error. With this fix, the error is immediately recognizable, categorized correctly, and consistent with established conventions from the broader JavaScript ecosystem.
