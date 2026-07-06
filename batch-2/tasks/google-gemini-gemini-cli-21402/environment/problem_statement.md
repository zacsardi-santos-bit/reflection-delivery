## Description

When the system connects to external agents over the network, there is no protection against a scenario where an agent's hostname resolves to a private or internal IP address. An attacker could configure a malicious agent whose DNS entry points to an internal service, causing the system to make requests to infrastructure it should never reach. This is a class of server-side request forgery vulnerability that should be addressed.

Additionally, the system receives agent configuration objects that describe how to connect to agents. These configuration objects can arrive in various inconsistent formats: some use one field name for protocol information, others use a different legacy field name; some URLs include a proper scheme, others are bare host:port strings. There is no central normalization step to resolve these inconsistencies before using the configuration.

## Expected Behavior

- Before connecting to an agent, the system should resolve the agent's hostname to its IP address via DNS and refuse connections when the resolved address falls within a private network range. An exception should be made for localhost and loopback addresses, which are commonly used during local development.
- The system should be able to produce the appropriate connection security credentials based on whether the agent URL uses a secure or plain protocol.
- A utility should be available to normalize incoming agent configuration objects into a consistent, predictable shape — filling in missing fields with safe defaults, unifying inconsistent protocol field names, and ensuring URLs are properly formed based on the protocol type.
- A utility should be available to cleanly separate an agent's base address from a well-known discovery path, so callers can work with the base URL independently.

## Why This Matters

Without hostname pinning and private IP validation, the system is vulnerable to being directed toward internal services. Without agent card normalization, subtle differences in agent configuration formats can cause silent failures or unexpected behavior when connecting to agents that follow different conventions.
