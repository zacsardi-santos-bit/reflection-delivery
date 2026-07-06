Enable snapshot testing support for the Web Test Runner in your DOM testing library. Update the snapshot assertion to detect the test runner environment and use the appropriate snapshot mechanism. Ensure that the assertion returns an awaitable result in the Web Test Runner environment.

*   Implement the snapshot assertion to:
    *   Return a Promise when running in the Web Test Runner environment.
    *   Detect the test runner environment:
        *   Use the Web Test Runner's asynchronous snapshot API when detected via `window.__WTR_MOCHA_RUNNER__`.
        *   Use the existing Karma snapshot mechanism when detected via `window.__mocha_context__` and `window.__snapshot__`.
        *   Throw an error if neither environment is detected, indicating the requirement for a supported test runner.
    *   Support both `expect` and `assert` chai interfaces for snapshot assertions.
    *   Throw an error with `actual` and `expected` properties when the current HTML does not match the stored snapshot.
    *   Support negation, ensuring no error is thrown when the current HTML differs from the stored snapshot.
    *   Accept an options object with `ignoreAttributes` and `ignoreTags` for comparison customization.

*   Update the `getMochaTestPath` function:
    *   Replace `snapshotPath` in `packages/semantic-dom-diff/src/utils.js`.
    *   Accept a mocha runnable object and handle hook context.
    *   Skip nodes with empty titles while walking the ancestor chain.
    *   Return path segments in top-down order.

*   Ensure the existence of a pre-populated snapshot file:
    *   Located at `packages/semantic-dom-diff/test-web/__snapshots__/chai-dom-equals-snapshots.test.snap.js`.
    *   Follow the Web Test Runner snapshot format.
    *   Include snapshot entries for specific test names with content starting with `<div>`.

*   Update `chai-dom-diff.js`:
    *   Modify the internal `assertHtmlEqualsSnapshot` function to return a Promise in the Web Test Runner environment.
    *   Derive snapshot names using `getMochaTestPath`.
    *   Use `getSnapshot` and `saveSnapshot` from `@web/test-runner-commands` for snapshot operations.
    *   Preserve the existing Karma path unchanged.
    *   Update import statements to use `getMochaTestPath`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.