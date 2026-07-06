## Description

When serialized cloud AI model configurations are deserialized by the load system, there is no built-in protection against server-side request forgery (SSRF) attacks. A malicious or untrusted serialized payload could include a custom network endpoint parameter (such as a URL override for the model's HTTP client) that, when the model configuration is instantiated, causes outbound requests to be directed to internal infrastructure endpoints — for example, cloud instance metadata services that expose credentials — or to arbitrary attacker-controlled URLs.

## Expected Behavior

- The deserialization system should have a class-specific validation layer that automatically blocks dangerous URL-override parameters for vulnerable cloud AI model integrations (including all known legacy aliases and module path variants).
- This protection should run regardless of whether a general-purpose validator callback has been supplied by the caller — including when no validator is passed at all.
- When a payload carries a dangerous URL-override parameter, deserialization should be rejected with a clear error indicating the SSRF risk and naming the offending parameter(s).
- When both built-in class-specific protection and a caller-supplied general validator are active, both should execute.
- Safe deserialization payloads (those without URL-override parameters) should continue to work normally.

## Why This Matters

Without this protection, an application that deserializes untrusted or externally-supplied model configurations is vulnerable to SSRF attacks. An attacker could craft a serialized payload that silently redirects all cloud model API calls to an internal metadata endpoint, potentially leaking credentials or accessing other internal services. Adding built-in, bypass-resistant protection closes this attack surface without requiring every caller to implement their own security checks.
