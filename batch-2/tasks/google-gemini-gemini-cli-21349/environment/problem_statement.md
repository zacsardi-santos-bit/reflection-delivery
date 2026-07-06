## Description

The CLI currently supports only a limited set of authentication methods (direct API key and Google login). There is no way for enterprise or gateway-based deployments to authenticate through an intermediary service that provides its own endpoint URL and custom request headers. This means users operating behind a corporate proxy or a custom gateway cannot use the tool.

## Expected Behavior

- A new gateway-based authentication option should appear in the list of available authentication methods.
- The gateway option should advertise its associated protocol metadata so clients can discover it.
- When a user selects the gateway authentication method and supplies a valid base URL (string) and request headers, the system should accept the configuration and complete authentication.
- If the gateway configuration is malformed — for example, if the base URL is not a valid string — the system should reject it with a clear error describing the problem.
- The underlying authentication refresh logic should be extended to accept the gateway endpoint URL and headers as optional parameters, so gateway-based connections can be properly configured.

## Why This Matters

Teams deploying this tool in environments that route traffic through a managed gateway currently have no supported authentication path. Adding gateway authentication as a first-class option lets these users connect without workarounds, while validation ensures misconfigured payloads are caught early with a helpful error.
