I'm working on adding per-mount content-based cache control to BuildKit's exec operation solver. The idea is that when computing a cache key for an exec operation, some mounts can safely have their cache key derived from the actual contents of the mounted data rather than just the structural description of the build. This is called content caching, and it's only safe for certain mount configurations.

I need the cache-map computation for exec operations to respect a new per-mount content-cache field that can be set to explicitly enable or disable this behavior, or left at a default that auto-detects safety. The rules for when content caching is safe should be: if the mount uses a root selector, or if the mount is read-only, or if the mount is marked as producing no output, then content caching is safe. If a mount is writable and scoped to a non-root subdirectory, then content caching is unsafe — requesting it explicitly should produce an error that makes clear the mount configuration is invalid, while the default mode should simply skip content caching rather than error.

Additionally, when content caching is explicitly turned off, it should always be disabled regardless of any other mount properties.

The protobuf definition for mounts also needs a new field to carry this content-cache setting with appropriate enum values.
