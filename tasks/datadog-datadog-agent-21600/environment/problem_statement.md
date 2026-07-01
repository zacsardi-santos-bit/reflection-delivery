## Description

The secrets resolution system currently tracks where each secret is used in configuration files using a flat string path. This loses critical information when multiple secrets appear within the same YAML array — all elements in the array end up sharing the same path, making it impossible to distinguish which specific array element contains which secret.

Beyond path tracking, the secrets system has no way to periodically refresh secrets after the initial resolution. Once a secret is resolved at startup, there is no mechanism to re-fetch it from the backend and propagate the new value to the running configuration. This makes it impossible to support secret rotation without restarting the agent.

## Expected Behavior

- When a secret appears inside a YAML array, its tracked path should include the numeric index of the array element so that different positions within the same array are distinguishable (e.g., the first element at index 0 and the second at index 1 both having their own path entries).
- A subscription mechanism should allow callers to register for notifications whenever a secret is resolved or its value changes, receiving the handle name, origin, full path, old value, and new value.
- A refresh operation should allow re-fetching all known secret handles from the backend and notifying subscribers only for handles whose value has actually changed.
- A per-handle allowlist should control which secrets are permitted to change during a refresh, so that only explicitly allowed secrets (such as API keys) can be updated at runtime.
- A utility for programmatically setting a config value at an arbitrary path (including into nested maps and slices) should support the secrets refresh workflow.

## Why This Matters

Without proper array indexing in paths, the debug output and change tracking for secrets is ambiguous when multiple secrets live in the same array. Without a refresh mechanism, secret rotation requires a full agent restart. These improvements allow the agent to support live secret rotation for explicitly allowlisted handles without service disruption.
