## Description

Python serverless functions on Vercel can authenticate with platform services by reading an identity token from a request header. However, in some deployment scenarios — particularly local development — the token is available only as an environment variable rather than arriving as a request header. In these situations, handler code that expects to read the token from a header finds nothing, even though the token is available in the environment.

## Expected Behavior

- When the identity token is set in the environment and an incoming request does not already carry the corresponding request header, the runtime should automatically inject the token as a header so handler code can access it uniformly.
- The environment variable token should only be used as a fallback: if the request already contains the header, the existing header value must be preserved without modification.
- An internal platform-level token header, when present, should take precedence over the environment variable value and be surfaced as the public header (with the internal header stripped).
- If the environment variable is absent or empty, no token header should be injected.

## Why This Matters

Handlers written to read the identity token from a request header work correctly in production (where the platform provides the header) but silently fail in local development (where only an environment variable is available). Bridging this gap lets developers write consistent handler code that works across both environments without manual workarounds.
