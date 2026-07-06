## Description

Odigos currently has no mechanism to differentiate between containers that use the standard glibc C library and those that use the musl C library (common in Alpine Linux images). This is a problem for .NET applications running on Alpine-based containers, because the instrumentation libraries for glibc and musl are fundamentally different and incompatible with each other.

When a .NET application runs in an Alpine (musl-based) container, Odigos should detect the C library variant used by the container's runtime and assign a separate, musl-specific instrumentation resource. Without this distinction, it is impossible to correctly instrument .NET workloads running on Alpine Linux.

## Expected Behavior

- The system should support a concept of a C library type (at minimum: glibc and musl variants).
- Functions that generate instrumentation plugin names and device names should accept an optional C library type indicator.
- When no C library type is provided, or when the standard glibc variant is used, the resulting names should be identical to the existing format (no change for the common case).
- When the musl variant is indicated, the resulting plugin name and device name should include a distinguishing prefix so they are treated as distinct resources.
- Parsing a musl-prefixed device name back into its language and SDK components should correctly recover the original language and SDK.

## Why This Matters

.NET applications running on Alpine Linux (which uses musl libc) cannot be correctly instrumented with glibc-linked native libraries. Supporting musl as a distinct C library type allows the instrumentation system to correctly select and apply the right instrumentation library variant for these workloads.
