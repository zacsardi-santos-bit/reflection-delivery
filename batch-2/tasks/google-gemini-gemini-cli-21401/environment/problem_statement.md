## Description

The CLI currently has no protection against server-side request forgery (SSRF) attacks. When the tool makes outbound HTTP requests based on user-provided or model-generated URLs, there is nothing preventing those requests from targeting internal network addresses, cloud instance metadata endpoints, or other reserved ranges that should never be reachable from an application serving external users.

## Expected Behavior

A new utility module should be added to the core package that provides:

- A synchronous check to determine whether a given IP address or hostname is private, reserved, or non-routable, covering standard private IPv4 ranges, RFC 6890 reserved ranges, private and link-local IPv6 ranges, IPv4-mapped IPv6 addresses where the embedded IPv4 is private, and special names like localhost.
- A synchronous check that extracts the hostname from a URL and determines if it is a private address.
- An asynchronous check that additionally performs DNS resolution on domain names, so that hostnames that point to private IPs are also blocked. If DNS resolution fails, the check should fail closed (i.e., treat the URL as unsafe rather than allowing the request).
- A DNS lookup wrapper that filters out private IPs from resolved results. If all resolved addresses are private, the lookup should signal an error.
- A safe fetch wrapper that rejects connections to private network addresses with a clear error message.
- A fetch utility that supports a timeout, and that also surfaces private network access errors with the offending URL included in the error message.

## Why This Matters

Without these protections, a crafted prompt or tool input could direct the CLI to exfiltrate data from internal services, access cloud metadata APIs (such as the well-known link-local metadata endpoint), or probe internal infrastructure. Adding SSRF protections ensures that outbound requests are safe by default.
