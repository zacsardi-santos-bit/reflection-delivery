## Description

The API gateway supports cross-origin resource sharing (CORS) configuration for APIs, allowing operators to specify allowed origins, methods, and request headers. However, there is currently no support for specifying which **response headers** the gateway should expose to browser clients making cross-origin requests.

According to the CORS specification, browsers restrict JavaScript access to response headers by default. Servers must explicitly list headers they want to expose via the `Access-Control-Expose-Headers` response header. Without this capability in the gateway, frontend applications cannot read custom response headers returned by backend APIs when making cross-origin requests.

## Expected Behavior

- Operators should be able to configure a list of expose headers in the CORS settings, both at the per-API level and globally at the gateway level.
- When expose headers are configured and a cross-origin request is processed, the gateway should include the `access-control-expose-headers` response header containing the configured header names.
- When no expose headers are configured, the `access-control-expose-headers` response header should not be set (or should be empty).
- The gateway's internal representation of CORS configuration must support this new field so that policy generation correctly reflects the configured expose headers.

## Why This Matters

Without this feature, API consumers building web applications cannot access custom response headers from cross-origin API calls, even when those headers are important for application logic (e.g., pagination metadata, rate limit information, or custom status fields).
