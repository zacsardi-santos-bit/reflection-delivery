Update the webpack test suite to replace the deprecated Pug template loader with a new, actively maintained alternative. Ensure the configuration and test cases reflect the new loader's requirements, and restrict tests to run only on supported Node.js versions.

*   Replace the webpack loader configuration for `.pug` files:
    *   Use `@webdiscus/pug-loader` instead of `pug-loader` as the loader identifier.
*   Update inline loader syntax for Pug templates:
    *   Use the explicit key-value form `self=true` for the self-mode option (e.g., `!@webdiscus/pug-loader?self=true!<template.pug>`).
*   Ensure correct rendering of Pug templates:
    *   With self-mode enabled and input `{ abc: "abc" }`, the output must be `"<p>selfabc</p><h1>included</h1>"`.
    *   Without self-mode and input `{ abc: "abc" }`, the output must be `"<p>abc</p><h1>included</h1>"`.
*   Restrict test execution based on Node.js version:
    *   Pug loader tests should only run on Node.js version 16 and above.
    *   Context loader tests should also only run on Node.js version 16 and above.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.