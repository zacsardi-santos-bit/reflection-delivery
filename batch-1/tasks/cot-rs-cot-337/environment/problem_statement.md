## Description

The session middleware in this web framework currently exposes very limited configuration options for session cookies — only whether the cookie should be marked as "Secure." Developers need a richer set of controls over how their session cookies behave, and all of these should be configurable through the existing TOML-based project configuration file rather than requiring code changes.

## Expected Behavior

The session middleware configuration (in the project config file under the middlewares/session section) should support the following additional options:

- **Same-site policy**: Control how the browser sends the cookie on cross-site requests. The available values should be "strict" (most restrictive, the default), "lax" (allows top-level cross-site navigations), and "none" (allows all cross-site requests).
- **HTTP-only flag**: Whether to restrict cookie access from JavaScript (enabled by default).
- **Cookie domain**: Optionally scope the cookie to a specific domain.
- **Cookie path**: Set the path scope for the cookie (defaults to the root path).
- **Cookie name**: Customize the session cookie name (defaults to a short identifier).
- **Always save**: Whether to always persist the session even when unmodified.
- **Expiry**: Control when the session expires — on browser close, after a period of inactivity (specified as a human-readable duration string such as "2h" or "30m"), or at a specific date and time (using a standard RFC 3339 timestamp).

When an expiry value cannot be parsed as a valid duration or timestamp, the configuration loading should return an error.

## Why This Matters

Without these options, developers have to work around the framework to customize session cookie behavior, or they're stuck with defaults that may not meet their security requirements. Fine-grained session cookie configuration is a standard feature of web frameworks and is especially important for applications with specific security policies around cross-site request handling and session lifetime.
