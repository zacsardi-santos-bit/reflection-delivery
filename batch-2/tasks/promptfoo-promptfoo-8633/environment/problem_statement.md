# Codex Default Provider Cache Does Not Isolate by Credential

## Description

The system that caches default Codex provider instances does not properly distinguish between different API credentials. When the active credential changes — for example, when a different API key is set in the environment — the old cached providers are returned instead of creating new ones bound to the new credential. This means calls can be silently routed through a provider configured for a different key than the one currently active.

There is also no upper bound on how many provider instances the cache holds. In long-running processes or automation scripts that rotate through many credentials, providers accumulate indefinitely with no cleanup, wasting resources.

Additionally, the credential availability check only considers one of the two recognized credential sources, making it inconsistent with how the underlying provider actually resolves credentials at call time.

## Expected Behavior

- Each distinct resolved API credential should receive its own isolated cached provider bundle.
- The credential that takes precedence (when both sources are present) should determine the cache partition — not the fallback slot.
- The cache should be bounded in size, with the oldest entry evicted when the limit is reached.
- Evicted providers should be shut down gracefully after a brief grace window, but only once any in-flight requests have completed.
- Callers holding a reference to an evicted and shut-down provider should still be able to make calls — the provider should transparently re-register itself and forward the call.
- After resurrection, evicted providers should re-enter the cleanup cycle so they do not accumulate indefinitely outside the cache.
- The credential availability check should recognize all supported credential sources, consistent with the provider's own resolution logic.

## Why This Matters

Without these fixes, credential rotation silently falls back to an old provider, re-registration leaks happen in long-running modes, and availability checks can incorrectly report no credentials when a valid one is present under an alternate source.
