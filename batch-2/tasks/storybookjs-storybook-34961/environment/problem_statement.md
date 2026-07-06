## Description

The open-service module currently lacks a unified server-side registry. Service creation and static asset generation are fragmented across separate entry points, which makes cross-service lookups during static builds impossible and forces callers to manually manage lists of service definitions.

We need a single server-side module that provides a global service registry. All services should be registered once and then automatically available to each other during runtime and during the static asset build phase.

## Expected Behavior

- A function to register a service definition into a global registry, returning the usable service instance
- A function to look up a registered service by its unique string identifier; if the identifier is unknown, the call should be rejected with a structured error (including a machine-readable code and a clear message)
- A function to synchronously retrieve the list of all currently registered services
- Functions to retrieve summary information (names of queries and commands) and full descriptors (including schema references) for registered services
- Registering the same service identifier twice must be rejected with a structured error
- Calling a query or command that has no handler must be rejected with a structured error identifying the missing operation
- Handlers and preload functions must be able to look up other registered services by ID, enabling cross-service composition during both runtime and the static build phase
- Service handlers can be omitted from the definition and supplied at registration time, allowing environment-specific implementations to be provided separately
- The static build function must operate on the global registry with no explicit service list, generating snapshot files and normalizing output paths (resolving relative prefixes, converting path separators)
- Paths in static output that attempt to escape the root directory must be rejected with a structured error
- A function to write static snapshot files to disk under a standard subdirectory of a given output directory, using pretty-printed JSON

## Why This Matters

Without a shared registry, services are isolated from each other during static builds, making it impossible to compose services that depend on one another. Consolidating registration into a single module eliminates the duplicated service-creation patterns and enables the full preload and composition capabilities that the open-service design requires.
