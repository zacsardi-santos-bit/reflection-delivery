## Description

Bruno's API spec panel includes a "try it out" feature that lets users send requests directly from a Swagger or OpenAPI document. Two gaps need to be addressed:

1. **No backend proxy for Swagger requests** — currently, requests made from the spec panel go out directly from the frontend, which means they bypass the user's configured proxy, TLS certificate settings, and consistent error handling. The requests need to be routed through the Electron main process so they benefit from the same infrastructure as regular Bruno requests.

2. **Silent or incorrect behavior for unsupported body types** — when the spec panel tries to serialize a request body that contains binary data, a file, or multipart form data, there is no clear handling. The app should detect these cases and provide a user-friendly message explaining that the body type isn't supported yet in Bruno and suggest alternatives (JSON, URL-encoded forms, plain text), rather than failing silently or crashing.

## Expected Behavior

- A backend proxy function should accept a request descriptor (URL, method, headers, body) and return either a normalized response object (with status, headers, and a base64-encoded body) or a structured error object with a machine-readable error code.
- The proxy should return a structured validation failure error when called without a URL, without making any network call.
- The proxy should surface network and TLS failures as structured error objects containing the error code and message.
- The proxy should normalize response headers to a plain object before returning (to ensure safe transmission across process boundaries).
- A body serialization utility should gracefully handle strings and URL-encoded parameters, return nothing for absent bodies, and throw an informative, typed error for unsupported binary body types (with an attached body-type label and machine-readable error code).

## Why This Matters

Without this proxy, the spec panel cannot reliably reach endpoints that require proxy settings or custom TLS certificates. And without proper body serialization error handling, users get confusing failures when trying to send multipart or binary requests through the panel.
