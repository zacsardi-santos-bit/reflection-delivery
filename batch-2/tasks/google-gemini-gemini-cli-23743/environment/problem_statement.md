## Description

When configuring a remote agent, users are currently required to provide a URL pointing to a hosted agent card endpoint. There is no way to embed the agent card content directly in the configuration — you always need to have the card served at a live URL. This is frustrating when testing locally or when the agent card content is already available and doesn't need to be fetched from a remote server.

## Expected Behavior

- Users should be able to specify a remote agent's card definition as an inline JSON string directly in the agent definition file (as an alternative to a hosted URL endpoint).
- When an inline JSON card is provided, the system should use it directly without making any network request to resolve the card.
- If the inline JSON string is malformed, the system should report a clear error at configuration load time identifying which agent's JSON is invalid.
- Specifying both a hosted URL and an inline JSON card for the same agent at the same time should be rejected with a validation error.
- The agent kind should be automatically inferred as "remote" when an inline JSON card is provided, just as it already is for URL-based card definitions.
- A remote agent definition must specify either a URL or an inline JSON card — having neither should be an error.

## Why This Matters

This makes it significantly easier to test and configure remote agents locally. Developers no longer need to stand up a live endpoint just to serve an agent card; they can embed the card content directly in the configuration file. It also enables use cases where the card content is generated or known ahead of time without requiring a network-accessible server.
