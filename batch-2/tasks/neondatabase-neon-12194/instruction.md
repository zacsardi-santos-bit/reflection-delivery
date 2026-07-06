Update the layer manager's lock acquisition methods to require an identifier for the lock holder. Implement a new enum to represent these identifiers, and modify all existing lock acquisition calls to use this new API.

*   Define a new enum `LayerManagerLockHolder` in `pageserver/src/tenant/timeline/layer_manager.rs`.
    *   Ensure `LayerManagerLockHolder` is publicly accessible from `crate::tenant::timeline::layer_manager`.
    *   Include at least a `Testing` variant for use in test code.

*   Update the `read` method on the layer manager lock type (`timeline.layers`):
    *   Change the method signature to `read(holder: LayerManagerLockHolder) -> impl Future<Output = LayerManagerReadGuard>`.
    *   Ensure it remains an async method.
    *   Require a `LayerManagerLockHolder` value as the first parameter.
    *   In test contexts, call this method as `timeline.layers.read(LayerManagerLockHolder::Testing).await`.

*   Update the `write` method on the layer manager lock type (`timeline.layers`):
    *   Change the method signature to `write(holder: LayerManagerLockHolder) -> impl Future<Output = LayerManagerWriteGuard>`.
    *   Ensure it remains an async method.
    *   Require a `LayerManagerLockHolder` value as the first parameter.
    *   In test contexts, call this method as `timeline.layers.write(LayerManagerLockHolder::Testing).await`.

*   Modify all existing callers of the `read` and `write` methods throughout the pageserver codebase:
    *   Pass an appropriate `LayerManagerLockHolder` variant to ensure successful compilation.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.