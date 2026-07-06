Implement a logging and caching mechanism for webpack's module concatenation optimization. Ensure that timing information is logged for each phase of the process and integrate caching to improve build performance.

*   Update `lib/optimize/ModuleConcatenationPlugin.js`:
    *   Change the `optimizeChunkModules` hook from synchronous (`tap`) to asynchronous (`tapAsync`).
    *   Obtain a logger via `compilation.getLogger("ModuleConcatenationPlugin")`.
    *   Emit timing with `logger.time()`/`logger.timeEnd()` for these phases in order: "select relevant modules", "sort relevant modules", "find modules to concatenate", "sort concat configurations", "create concatenated modules".
    *   Emit exactly two `logger.debug()` calls for hidden lines in verbose stats output.
    *   Replace `ConcatConfiguration` class's `clone()`/`set()` with `snapshot()`/`rollback(snapshot)`.
    *   Modify `_tryToAdd` method to accept a `candidates` (Set) parameter and add discovered imports to it.

*   Update `lib/optimize/ConcatenatedModule.js`:
    *   Change the constructor to accept an options object with fields `{ identifier, rootModule, modules, orderedConcatenationList }`.
    *   Add a static factory method `createFromModuleGraph(rootModule, modules, moduleGraph, associatedObjectForCache)`.
    *   Implement `build(options, compilation, resolver, fs, callback)` to populate `buildInfo` from constituent modules.
        *   Initialize `buildInfo.cacheable` to `true`, set to `false` if any module is not cacheable.
        *   Do not forward presentational dependencies from constituent modules.
    *   Add `updateCacheModule(module)` to copy `_identifier`, `rootModule`, `_modules`, and `_orderedConcatenationList`.
    *   Implement `serialize(context)` and `deserialize(context)` for `_cachedCodeGenerationHash` and `_cachedCodeGeneration`.
    *   Add a static `deserialize(context)` factory.
    *   Call `makeSerializable(ConcatenatedModule, "webpack/lib/optimize/ConcatenatedModule")` after class definition.

*   Update `lib/util/internalSerializables.js`:
    *   Register `"optimize/ConcatenatedModule": () => require("../optimize/ConcatenatedModule")` in the exported map.

*   Update `lib/Compilation.js`:
    *   Change `optimizeChunkModules` hook from `SyncBailHook` to `AsyncSeriesBailHook`.
    *   Modify `seal()` method to call `optimizeChunkModules.callAsync(...)` and place subsequent logic in the async callback.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.