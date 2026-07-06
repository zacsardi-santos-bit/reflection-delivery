## Description

When building Lambda container image functions with SAM CLI, users currently have no way to opt into using the native CLI-based build engine provided by their container runtime. The existing build path always uses the SDK-based method, which lacks support for newer build features and more advanced image construction scenarios. Users who have Docker's buildx plugin installed, or who are using Finch, should be able to take advantage of their runtime's CLI tooling for image builds rather than being locked into the SDK path.

## Expected Behavior

- A new optional flag should be available on the build command to enable CLI-based image building.
- When the flag is enabled, SAM CLI should detect the active container runtime and invoke its CLI tooling to build container images.
- If the required CLI tooling is not available (for example, the buildx plugin is missing), SAM CLI should emit a clear, descriptive error rather than failing silently or with a confusing message.
- When the flag is not set, the existing SDK-based build behavior should remain unchanged as the default.

## Why This Matters

As container build tooling matures, users need more flexibility in how their images are constructed. CLI-based build tools offer richer feature sets (better caching, provenance, cross-platform builds) that the SDK abstraction doesn't expose. Adding this opt-in flag lets users take advantage of those capabilities while keeping existing workflows unaffected.
