I'm working on a provider caching system that keeps default AI provider instances around so they don't need to be recreated on every call.

*   getCodexDefaultProviders(env?) must return different cached provider bundle instances when different API credentials are used. The returned gradingProvider's apiKey property must equal the credential used to create the bundle, and getApiKey() must return the resolved key.

*   getCodexDefaultProviders(env?) must return the same cached provider bundle instance when called multiple times with the same resolved API credential.

*   The cache must be partitioned by the resolved API key: OPENAI_API_KEY takes precedence over CODEX_API_KEY. Two calls that differ only in a CODEX_API_KEY override but share the same OPENAI_API_KEY must hit the same cached bundle. Rotating OPENAI_API_KEY must create a new cache entry.

*   The provider bundle cache must be bounded to a maximum of 32 entries (LRU). When a 33rd unique credential would create a new entry, the least-recently-used existing entry must be evicted.

*   Evicted provider bundles must have shutdown() called on each unique provider (gradingProvider, gradingJsonProvider, webSearchProvider). Shutdown must be deferred: it fires only after a grace-period timer elapses and no callApi calls are in flight on that bundle.

*   If a callApi call is in flight when an eviction grace timer fires, the shutdown must be delayed until the call completes and the timer fires again (i.e., the timer is re-armed after each call finishes as long as the bundle is eviction-pending and has no active calls).

*   Sequential callApi calls on an evicted bundle must complete successfully during the grace period (before the timer fires). Shutdown must not be called until the grace timer fires with no active calls.

*   hasCodexDefaultCredentials(env?) must return true when either CODEX_API_KEY or OPENAI_API_KEY is set in the environment (not only CODEX_API_KEY). This mirrors how the provider itself resolves credentials.

*   If callApi is invoked on a provider from a bundle that has already completed its eviction shutdown, the framework must re-register all unique providers in the bundle with the provider registry (exactly one register call per unique provider: gradingProvider, gradingJsonProvider, webSearchProvider), then forward the call, which must resolve successfully.

*   After a shutdown-evicted bundle is resurrected by a callApi call, the bundle must re-enter the eviction cycle: once the call finishes and the grace timer fires, shutdown() must be called again on all providers in the bundle.

*   clearCodexDefaultProvidersForTesting() must cancel all pending shutdown timers and mark all tracked bundles so that any in-flight callApi finally blocks cannot re-arm shutdown timers after cleanup. After cleanup and call completion, no timer must remain pending and shutdown must not be called.

*   A createDeferred<T>() utility must exist at test/util/utils.ts. It returns an object with a promise property (Promise<T>) and resolve / reject functions that settle that promise.


*   Interface details: Type: Function
Name: getCodexDefaultProviders
Location: src/providers/openai/codexDefaults.ts
Signature: getCodexDefaultProviders(env?: EnvOverrides) -> CodexDefaultProviders
Description: Returns (or creates and caches) a bundle of default Codex provider instances for the resolved API credential derived from env. Different resolved credentials produce different cached bundles; the same resolved credential returns the same cached instance. The cache is LRU-bounded to 32 entries; evicted bundles are scheduled for graceful shutdown. Exported function.

Type: Function
Name: hasCodexDefaultCredentials
Location: src/providers/openai/codexDefaults.ts
Signature: hasCodexDefaultCredentials(env?: EnvOverrides) -> boolean
Description: Returns true when a valid Codex credential is available. Must return true when either CODEX_API_KEY or OPENAI_API_KEY is present (not only CODEX_API_KEY), mirroring the resolution order used by the provider itself. Exported function.

Type: Function
Name: clearCodexDefaultProvidersForTesting
Location: src/providers/openai/codexDefaults.ts
Signature: clearCodexDefaultProvidersForTesting() -> void
Description: Clears all cached and evicted provider bundles, cancels any pending shutdown timers, and marks all tracked bundles as cancelled so that in-flight callApi finally blocks cannot re-arm shutdown timers after this call returns. Exported function used in test teardown.

Type: Function
Name: createDeferred
Location: test/util/utils.ts
Signature: createDeferred<T>() -> { promise: Promise<T>; resolve: (value: T | PromiseLike<T>) => void; reject: (reason?: unknown) => void }
Description: Creates a deferred promise whose resolution or rejection can be triggered externally. Used by tests to simulate and control in-flight asynchronous operations.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.