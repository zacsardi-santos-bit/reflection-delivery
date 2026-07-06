## Description

The built-in HTTP server in wasmCloud currently supports only address-based routing: every request arriving at a given listen address is forwarded to a single component. There is no way to have one HTTP listener serve multiple components simultaneously based on the URL path of incoming requests.

This limits flexibility for multi-component deployments that need to share a single port. A common pattern is to route different URL path prefixes to separate components — but today all traffic goes to whichever single component is linked to the address.

Additionally, the test infrastructure does not provide a utility for stopping a provider and verifying it has fully shut down, making it difficult to test lifecycle behavior in integration tests.

## Expected Behavior

- The built-in HTTP server should support a "path" routing mode in addition to the existing address-based mode.
- When operating in path routing mode, individual URL paths should be mappable to different components via the link configuration.
- Path registrations should be dynamic: adding a link registers the path, removing a link deregisters it.
- A request to an unregistered path should return a 404 response.
- After a link is removed and the path is no longer registered, requests to that path should return 404.
- After re-adding a link, requests to the previously deregistered path should succeed again.
- When the provider is stopped entirely, requests to the address should fail outright (not just 404).
- A test utility function should exist for stopping a provider and confirming it stopped.

## Why This Matters

Many applications need to run multiple isolated components behind a single HTTP endpoint. Without path routing, users must dedicate a separate port for each component or build a custom routing layer. This change makes it straightforward to compose multi-component applications on a single listen address using the standard wasmCloud link system.
