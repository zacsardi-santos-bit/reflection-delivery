## Description

The plugin list and plugin install APIs return policy fields — specifically the authentication policy and installation policy — as optionally present values that can be null. In practice, these fields always have concrete values because every plugin has a meaningful policy. The optional/nullable representation creates unnecessary ambiguity for clients consuming these APIs, forcing them to handle a null case that never actually occurs.

## Expected Behavior

- When listing plugins, each plugin entry in the response must include both the installation policy and authentication policy as always-present, non-null fields.
- When installing a plugin, the install response must include the authentication policy as an always-present, non-null field.
- Clients should be able to read these policy fields without needing to unwrap or guard against a missing value.

## Why This Matters

Making these fields always-present reflects reality (they are never actually absent) and simplifies the API contract for consumers. It removes a source of confusion and defensive handling that serves no purpose. Any client that previously had to handle a null policy can now rely on a guaranteed value being present.
