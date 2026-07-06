## Description

The SDK currently exposes approval behavior through two separate low-level parameters on thread and turn operations. Callers must understand internal approval policy concepts and a separate reviewer field to configure this correctly, which is confusing and error-prone. These two parameters should be replaced with a single, high-level option that clearly communicates the intent.

## Expected Behavior

- A new high-level enumeration with two named options should replace the two existing approval parameters across all thread and turn operations:
  - One option that denies all escalated permission requests
  - One option that routes permission requests to automatic review
- New thread operations should default to the automatic review mode so permission requests are handled gracefully without any explicit configuration.
- Operations on existing threads (resuming, forking, or running subsequent turns) should default to preserving whatever approval settings are already in place, without overriding them, unless the caller explicitly specifies a mode.
- Passing an unrecognized value should produce a clear error message listing the valid options.
- The new enumeration must be part of the package's public API surface.

## Why This Matters

Developers should not need to know about internal policy models or reviewer configurations to express a simple intent like "deny all escalations" or "let the system handle it automatically." A single named option that maps to the correct underlying behavior is much easier to use correctly and harder to misuse.
