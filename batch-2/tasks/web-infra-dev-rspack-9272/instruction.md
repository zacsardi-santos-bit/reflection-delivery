Update the project to replace the outdated pug template loader with a modern alternative. Ensure all references to the old loader in package files and test configurations are updated, and adjust the expected output for specific rendering scenarios.

*   Replace `pug-loader` with `@webdiscus/pug-loader` at version `^2.11.1` in the following files:
    *   `tests/plugin-test/package.json`
    *   `tests/webpack-test/package.json`
    *   `packages/rspack-test-tools/package.json`
*   Update module rule references:
    *   In `packages/rspack-test-tools/src/processor/normal.ts`, change the loader for `.pug` files to `"@webdiscus/pug-loader"`.
    *   In `tests/webpack-test/TestCases.template.js`, update the loader for `/\.pug/` files to `loader: "@webdiscus/pug-loader"`.
*   Modify inline loader syntax:
    *   In `packages/rspack-test-tools/tests/configCases/plugins/html-webpack-plugin/rspack.config.js`, use `"@webdiscus/pug-loader!"` as the inline loader prefix.
*   Ensure correct rendering output:
    *   When using the inline loader syntax `!@webdiscus/pug-loader?self!` with data `{ abc: "abc" }`, the output should be `"<p>abc</p><h1>included</h1>"`.
    *   When using module rules without inline options and the same data, the output should also be `"<p>abc</p><h1>included</h1>"`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.