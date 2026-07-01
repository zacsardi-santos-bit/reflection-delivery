## Description

The platform needs a dedicated CAPTCHA verification service that can be dropped into the request pipeline to protect endpoints from automated bot traffic. Currently there is no standardized way to validate anti-bot tokens attached to incoming requests, meaning form submissions and similar endpoints are unprotected.

## Expected Behavior

- The service should be configurable: it can be enabled or disabled, and it should accept a secret key used to verify tokens with the external bot-detection provider as well as a score threshold that determines when traffic is considered suspicious.
- When enabled, the service should produce request middleware that inspects the anti-bot token supplied with each request.
- If no token is present in the request, the middleware should reject the request with an appropriate error message.
- If the external verification call fails for any reason, the failure should be handled gracefully, producing a descriptive error rather than crashing the server.
- If the verification score meets or exceeds the configured threshold (indicating likely bot activity), the request should be blocked. The error returned to the caller should be a generic server error message to avoid leaking internal details.
- If the score is below the threshold, the request should proceed normally with no error.
- When the service is disabled, the middleware should be a no-op that passes every request through without performing any check.

## Why This Matters

Without this service, bot-generated traffic can reach sensitive endpoints unchecked. A reusable, configurable middleware layer makes it straightforward to add CAPTCHA protection to any route without duplicating verification logic across the codebase.
