Refactor the server context to allow the cache to be injected as an external parameter rather than being created internally. Implement a shared cache interface as a trait, create a native in-memory cache implementation, and provide a helper function for constructing this cache. Ensure all existing tests continue to pass with these changes.

*   Modify the server context constructor:
    *   Update `AppContext::new` in `src/app_context.rs` to accept five parameters: `blueprint`, `h_client` (Arc<Http>), `h2_client` (Arc<Http>), `env` (Arc<Env>), and `cache` (Arc<EntityCache>).
    *   Ensure the `cache` parameter is provided externally and not created internally.

*   Define a shared cache interface:
    *   In `src/lib.rs`, define a `Cache` trait as an async trait with associated types `Key` (Hash + Eq) and `Value`.
    *   Implement the following methods in the `Cache` trait:
        *   `async fn set<'a>(&'a self, key: Self::Key, value: Self::Value, ttl: NonZeroU64) -> anyhow::Result<Self::Value>`
        *   `async fn get<'a>(&'a self, key: &'a Self::Key) -> anyhow::Result<Self::Value>`
    *   Ensure the `Cache` trait is `Send + Sync`.

*   Implement a native in-memory cache:
    *   Define a struct `NativeChronoCache` in `src/cli/cache.rs` that implements the `Cache` trait using a TTL-based in-memory store.
    *   Provide a `new()` constructor for `NativeChronoCache`.
    *   Ensure `NativeChronoCache` is publicly accessible as `tailcall::cli::cache::NativeChronoCache`.

*   Provide a helper function:
    *   In `src/cli/mod.rs`, implement and publicly export a function `init_chrono_cache` that returns a `NativeChronoCache` instance.
    *   Ensure the return value implements the `Cache` trait and can be wrapped in `Arc`.

*   Update type definitions:
    *   Define a type alias `EntityCache` in `src/lib.rs` as `dyn Cache<Key = u64, Value = ConstValue>`.
    *   Use `Arc<EntityCache>` for the cache field in both `AppContext` and `RequestContext`.

*   Update `RequestContext`:
    *   Store the cache field as `Arc<EntityCache>`.
    *   Make `cache_get` and `cache_insert` methods async, delegating to the `Cache` trait methods, and returning `Option<ConstValue>`.

*   Remove old caching mechanism:
    *   Ensure `src/chrono_cache.rs` is no longer used for caching in `AppContext` and `RequestContext`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.