## Description

Several promptfoo features always send their API requests to the public cloud endpoint, completely ignoring the custom on-premises host that users have configured. This means customers running promptfoo against their own self-hosted deployment cannot use guardrails, email status checks, or the HTTP provider generator — these features silently bypass their configured host and hit the public API instead.

Additionally, when users configure a host URL with a trailing slash (which is a natural habit and common convention), API calls produce malformed double-slash URLs that fail with unexpected errors.

## Expected Behavior

- When a user has configured a custom cloud or on-premises host, all API calls (guardrail checks, email status, HTTP provider generation) should be routed to that configured host, not the default public endpoint.
- Requests to the configured on-premises host should include the appropriate bearer token for authentication.
- Host URLs that end with a trailing slash should be silently normalized — the slash should be stripped when stored and when used to construct URLs, so that callers never end up with double-slash paths.
- When no custom host is configured, behavior should remain unchanged: requests go to the default public endpoint without any authentication token.
- An explicit environment variable override for the remote API base URL should still win over the configured cloud host, without leaking any authentication credentials to the override endpoint.

## Why This Matters

On-premises and private cloud deployments of promptfoo are a supported use case, but these routing bugs effectively break several core features for those users. Fixing the trailing-slash normalization also prevents subtle, hard-to-debug failures that crop up from simple configuration formatting differences.
