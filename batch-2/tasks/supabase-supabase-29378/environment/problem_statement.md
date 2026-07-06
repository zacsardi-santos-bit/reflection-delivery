## Description

The documentation site needs a secure API endpoint that external content pipelines can call to trigger cache revalidation. Currently there is no protected mechanism for this, so stale cached docs cannot be reliably purged by external services.

## Expected Behavior

- The endpoint must verify that requests carry a recognized API key before performing any cache operations.
- Requests without an authorization token should be rejected outright.
- Requests with an unrecognized token should also be rejected.
- Malformed request bodies (e.g., missing the list of cache tags to revalidate) should be rejected with an appropriate error.
- For standard API keys, a cooldown of 6 hours must be enforced between successive revalidations: if a revalidation has already happened within the last 6 hours, the request should be rate-limited.
- For privileged API keys, the 6-hour cooldown should be bypassed so that trusted callers can always trigger revalidation.
- On a successful revalidation, the endpoint should purge the cache for each requested tag and record the event so the cooldown can be enforced in future requests.

## Why This Matters

External content pipelines need a reliable, auditable way to keep the documentation cache fresh. Without this endpoint, stale content can persist in the cache indefinitely, and there is no way for automated systems to invalidate specific cache tags on demand while still protecting against accidental over-purging.
