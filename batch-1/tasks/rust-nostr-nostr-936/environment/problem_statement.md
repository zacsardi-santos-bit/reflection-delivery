## Description

The NIP-05 identity resolution functionality in this library is currently tightly coupled to HTTP networking and locked behind an optional feature flag. This makes it impossible to use the parsing and profile extraction logic in offline scenarios, unit tests with static data, or when an application wants to manage HTTP connections itself with a different client. The entire feature is unavailable unless a specific cargo feature is enabled, which limits its accessibility.

## Expected Behavior

- Developers should be able to parse a NIP-05 address string (in "name@domain" format) into a structured type that they can pass around.
- Developers should be able to supply pre-fetched or test-fixture JSON data directly to the library and receive back the resolved public key and relay information — no network call should be required from the library itself.
- The NIP-05 types and parsing logic should be available by default, without needing to enable any optional feature flag.

## Why This Matters

Decoupling the HTTP fetching from the data parsing makes the code more testable, more composable, and usable in environments where direct network access from the library is not acceptable or not possible (e.g., embedded, WASM, or async-runtime-agnostic contexts). Any application should be able to use its own HTTP client, fetch the JSON, and hand it to the library for parsing.
