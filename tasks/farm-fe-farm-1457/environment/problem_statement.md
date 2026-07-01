## Description

The module resolver currently has no way to accept per-invocation configuration — every resolution call relies entirely on the global compilation settings. This makes it impossible for different callers (e.g., a CSS plugin vs. a JavaScript plugin) to customize resolution behavior on a per-call basis without mutating global state.

As a result, features like dynamically overriding which file extensions to try when resolving a module cannot be implemented cleanly. Similarly, the package metadata cache uses only the file path as a lookup key, which means two resolution attempts for the same path but with different options could incorrectly share a cached result.

## Expected Behavior

- The resolver's main resolution method should accept a per-call options parameter that callers can use to pass call-specific configuration.
- Callers that do not need any special behavior should be able to use a default (no-op) options value with zero configuration.
- The package metadata cache should incorporate the per-call options into its cache key, so that different option values for the same path produce distinct cache entries.
- A public method should be available on the package metadata loader to compute the cache key from a path and options, which tests and callers can use to verify cache state.

## Why This Matters

Without per-call options, plugins that need to customize resolution behavior (such as trying additional file extensions based on context) must work around the global configuration, leading to brittle behavior and incorrect dependency resolution. The cache key fix prevents stale or incorrect cache hits across resolution calls with different configurations.
