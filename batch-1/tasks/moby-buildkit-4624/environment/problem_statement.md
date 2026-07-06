## Description

BuildKit can optimize caching by analyzing the actual contents of mounted filesystems rather than relying solely on build instruction metadata. However, this content-based cache analysis is only reliable when a mount's content cannot change during the build step — applying it to a writable, non-root-scoped mount could produce incorrect cache results.

Currently, there is no per-mount mechanism to control whether content-based cache checking is applied. This makes it impossible for users to explicitly opt a mount into or out of content caching, and the system cannot automatically enable the optimization for mounts where it would be safe.

## Expected Behavior

- Each mount should carry an explicit setting that controls content-based caching: an "off" mode that always disables it, an "on" mode that enables it when safe and errors when unsafe, and a default mode that auto-detects safety.
- In default mode, content caching should be enabled automatically for read-only mounts, no-output mounts, and mounts scoped to the full root of their source — these are configurations where content cannot be unexpectedly modified.
- In default mode, content caching should NOT be enabled automatically for writable mounts that are scoped to a subdirectory, including mounts at the root destination path.
- When the "on" mode is requested for an unsafe mount (writable and scoped to a non-root subdirectory), the operation should be rejected with a clear error indicating the mount configuration is invalid rather than silently misbehaving.
- When the "off" mode is set, no content-based cache analysis should be performed regardless of other mount properties.

## Why This Matters

Without this, users have no way to harness content-based caching for individual mounts, and there's no safety check to prevent incorrect use of this optimization. Build correctness could be silently compromised if content caching were applied to mounts whose contents can change.
