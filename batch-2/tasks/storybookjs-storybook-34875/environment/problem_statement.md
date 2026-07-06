## Description

We need a unified server-side registry for our service infrastructure. Right now, services have to be instantiated by passing their definitions directly to a factory function each time, which means there's no global place to look up a service by name. This also means cross-service interactions — where one service's handler needs to call another service — require manual wiring at the call site.

Static file generation has the same problem: you have to pass an explicit list of service definitions every time you want to build snapshots, there's no way for services participating in the static build to look up other services, and there is no path safety validation.

## Expected Behavior

- Services should be registered globally by a dedicated registration function and retrievable by their string ID anywhere (including from within other service handlers).
- Registering the same service ID twice should be rejected with a clear error.
- Looking up a service ID that was never registered should also produce a clear error.
- Calling a query or command whose handler was not provided should produce an informative "not implemented" error rather than crashing silently.
- Registration should optionally accept handler implementations for operations that have no handler in the service definition, enabling the server to supply environment-specific behavior.
- Introspection helpers should allow listing all registered services (with their operation names) and retrieving a full descriptor for a single service including its input/output schemas.
- Building static files should automatically use all registered services rather than requiring an explicit list. Services that have no static configuration should be silently skipped.
- Static build paths provided by a service must be normalized to consistent slash-separated keys, and any path that would escape the output root (using directory traversal) must be rejected with a clear, actionable error message.
- A dedicated function should write the built snapshots to disk under a designated output directory, creating any needed subdirectories.

## Why This Matters

Without a registry, cross-service dependencies during static builds are impossible to express. A shared registry enables service isolation while still allowing services to discover and call one another, and it provides a consistent, safe mechanism for writing the static output to disk.
