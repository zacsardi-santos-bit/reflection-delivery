Implement the specified changes to improve the region management layer of the storage engine. Ensure that error codes are accurate, interfaces are simplified, and unnecessary error handling is removed.

*   Create a new struct `ManifestContext` in `src/mito2/src/region.rs`.
    *   Encapsulate the manifest manager and region state.
    *   Use a `tokio::sync::RwLock<RegionManifestManager>` named `manifest_manager`.
    *   Use an `AtomicCell<RegionState>` named `state`.
    *   Implement a test-only async method `manifest()` to return the current region manifest.
        *   Compile this method only in `#[cfg(test)]` builds.
        *   Access it in tests as `region.manifest_ctx.manifest().await`.

*   Define a type alias `ManifestContextRef` in `src/mito2/src/region.rs`.
    *   Use `pub(crate) type ManifestContextRef = Arc<ManifestContext>`.

*   Update the `MitoRegion` struct in `src/mito2/src/region.rs`.
    *   Replace `manifest_manager` and `writable` fields with `manifest_ctx: ManifestContextRef`.

*   Define an enum `RegionState` in `src/mito2/src/region.rs`.
    *   Include variants: `ReadOnly`, `Writable`, `Altering`, `Dropping`, `Truncating`, `Editing`.

*   Modify the error handling for non-writable regions.
    *   Return `StatusCode::RegionNotReady` instead of `StatusCode::RegionReadonly` when a write is attempted on a non-writable region.

*   Update the `RegionManifestManager::stop()` method in `src/mito2/src/manifest/manager.rs`.
    *   Change the return type to unit `()` to make it infallible.
    *   Ensure callers do not need to call `.unwrap()` or handle an error.

*   Update error handling in `src/mito2/src/error.rs`.
    *   Replace the `RegionReadonly` error variant with a new `RegionState` error variant.
    *   Map this error to `StatusCode::RegionNotReady` in the `status_code()` method.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.