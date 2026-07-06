## Description

When Babel transforms code that already has an associated source map — a common scenario in multi-pass build pipelines where each transformation step produces its own map — it needs to merge the input map with the newly generated output map. The current implementation may return a specialized object from the underlying remapping library rather than a standard plain data object. This causes failures in Babel's traversal and type utilities, which inspect source maps as plain data and report unexpected object type errors.

A dedicated source map merging module should be added to the core transformation file-generation pipeline. The merged map it returns must be a plain data object so that downstream Babel code can process it without type errors.

Additionally, an old test infrastructure workaround that spawned separate Node.js child processes to test ESM plugin and configuration loading should be removed. That workaround existed because of a Node.js bug that caused crashes when using dynamic imports inside the test runner. Since that bug has since been fixed, the tests can call Babel's transform and config-loading APIs directly in-process.

## Expected Behavior

- A source map merging utility should be added under the core transformation file-generation code
- The utility accepts an input source map, an output source map, and a source file name, and returns a merged map
- The returned merged map must be a plain JavaScript object — not a class instance or any other specialized type — so that Babel's internal utilities can work with it correctly
- The test helper that spawned child processes for ESM loading tests should be removed; those tests should call Babel's APIs directly

## Why This Matters

Build pipelines that chain multiple Babel transformations depend on correct source map merging. If the merged map is returned as a class instance instead of a plain data object, tools that inspect the map will fail. Removing the spawn workaround also simplifies the test infrastructure and aligns tests with how Babel is actually used.
