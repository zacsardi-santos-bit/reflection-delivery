Implement a fix for the bundler to ensure that when a CommonJS module conditionally re-exports another module at runtime, the bundled output correctly uses the interop compatibility layer. Ensure that the generated code accesses the actual exported value through the interop layer, preserving the `.default` property access.

*   Ensure that when a CommonJS module uses a conditional require expression as its module export value, and an ESM file imports it as a default import, the bundled output accesses the imported value via the `.default` property on the interop-converted namespace object.
*   In the scenario where `commonjs.js` conditionally exports modules using `module.exports = condition ? require('./a.js') : require('./b.js')` and `main.js` imports it with `import plus from './commonjs.js'`:
    *   Wrap each CommonJS module with `__commonJS`.
    *   Convert the namespace with `__toESM`.
    *   Rewrite the function call to `import_commonjs.default.call({}, 1, 2)` in the bundled output.
*   When accessing a named property on an ESM-interop-wrapped CommonJS module namespace, ensure the bundled output accesses the property through `.default`, producing `import_foo.default.bar`.
*   Verify the existence of the test fixture directory `crates/rolldown/tests/rolldown/cjs_compat/issue-3364/` with the following files:
    *   `_config.json` (empty config)
    *   `a.js` and `b.js` (each exporting a function via `module.exports`)
    *   `commonjs.js` (conditional require as the export)
    *   `main.js` (ESM default import and assertion)
    *   `artifacts.snap` (snapshot of expected bundled output)
*   Confirm that the `artifacts.snap` snapshot for `issue-3364` shows `import_commonjs.default.call({}, 1, 2)` in the `main.js` region of the bundled output.
*   Ensure the `artifacts.snap` snapshot for `bundler_esm_cjs_tests/26` shows `import_foo.default.bar === void 0` in the entry.js region, correcting the previously incorrect `import_foo.bar === void 0` form across all output format variants.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.