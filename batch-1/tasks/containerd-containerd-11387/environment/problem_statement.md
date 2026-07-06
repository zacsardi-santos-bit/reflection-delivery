## Description

The project currently uses an outdated version of its HTTP/2 networking library that does not recognize the extended connect protocol setting. When code interacts with HTTP/2 settings related to the extended connect protocol — a feature that enables tunneling non-HTTP protocols such as WebSockets over HTTP/2 — the library returns a generic "unknown" label for this setting rather than its proper standardized name.

This is a problem because the extended connect protocol is an important modern HTTP/2 feature (described in RFC 8441), and the inability to correctly identify this setting means the project cannot properly support use cases that depend on it.

## Expected Behavior

- The HTTP/2 networking library used by the project should recognize the extended connect protocol setting by its standardized name.
- When the string representation of this HTTP/2 setting identifier is requested at runtime, it should return the correct standardized name rather than falling back to an unknown-setting placeholder.

## Why This Matters

Supporting the extended connect protocol is necessary for enabling tunneling use cases over HTTP/2. Without properly recognizing this setting, the project cannot accurately log, debug, or implement behavior tied to this protocol feature. Updating the HTTP/2 networking dependency to a version that includes this definition resolves the issue.
