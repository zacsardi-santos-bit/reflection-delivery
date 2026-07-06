I'm working on Babel's core transformation pipeline and I've run into two related issues.

*   A source map merging function must be implemented as the default export of packages/babel-core/src/transformation/file/merge-map.ts, compiled to packages/babel-core/lib/transformation/file/merge-map.js.

*   The merging function must accept three parameters: an input source map object, an output source map object, and a source file name string.

*   The function must return a plain JavaScript object. The returned value must satisfy: typeof result === 'object', Object.prototype.toString.call(result) === '[object Object]', and Object.getPrototypeOf(result) === Object.prototype. It must NOT be a class instance or any specialized subtype.

*   A test file packages/babel-core/test/merge-map.skip-bundled.js must exist. It imports the mergeSourceMap function from ../lib/transformation/file/merge-map.js using the pattern `_mergeSourceMap.default || _mergeSourceMap`, calls it with an input map, an output map, and a source file name, and asserts the returned object is a plain JavaScript object.

*   The spawn-based test helpers (test/helpers/esm.js and the fixture scripts babel-compile-async.mjs, babel-compile-async-parallel.mjs, babel-compile-sync.mjs, babel-load-options-async.mjs) must be removed. The tests in async.js and config-chain.js must call babel.transformSync, babel.transformAsync, and babel.loadOptionsAsync directly rather than via the spawn wrappers.

*   The pfs proxy workaround in config-chain.js must be replaced with a direct import of node:fs/promises.


*   Interface details: Type: Function
Name: mergeSourceMap (default export)
Location: packages/babel-core/src/transformation/file/merge-map.ts (compiled to packages/babel-core/lib/transformation/file/merge-map.js)
Signature: mergeSourceMap(inputMap: SourceMap, map: SourceMap, sourceFileName: string): SourceMap
Description: Merges an input source map with a generated output source map using the provided source file name. Must return a plain JavaScript object whose prototype is exactly Object.prototype (i.e., not a class instance, not a subtype). The compiled output at lib/transformation/file/merge-map.js must be accessible as either a default export or a direct export (the test uses `_mergeSourceMap.default || _mergeSourceMap`).

Type: File
Name: merge-map.skip-bundled.js
Location: packages/babel-core/test/merge-map.skip-bundled.js
Description: Test file that the benchmark test command runs. It imports mergeSourceMap from ../lib/transformation/file/merge-map.js and verifies the return value is a plain JavaScript object. This file must exist and its test must pass when the implementation is correct.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.