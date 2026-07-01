## Add Support for Title Case HTTP/1.1 Header Names

### Description

The HTTP client currently normalizes all outgoing header names to lowercase when making HTTP/1.1 requests. While this is technically valid per the HTTP specification (header names are case-insensitive), some legacy servers or middleware in the wild enforce a specific expectation of "title case" header names, where the first letter of each hyphen-separated word is capitalized.

There is currently no way for users to opt into this behavior, which means the library cannot interoperate with those non-standard servers.

### Expected Behavior

- Users should be able to configure the HTTP client to send HTTP/1.1 headers in title case format (e.g., "X-Custom-Header" instead of "x-custom-header") at the wire level.
- This should be a per-client configuration option set on the client builder.
- By default, the existing behavior (lowercase header names) should be preserved so that no existing code is affected.
- When enabled, the title case conversion should apply consistently to all headers in the outgoing request.

### Why This Matters

Some servers out in the wild do not correctly implement the HTTP specification's requirement that header names be treated case-insensitively. Adding this option allows users of the library to work around those servers without sacrificing compatibility for everyone else.
