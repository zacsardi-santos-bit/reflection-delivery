Implement a dedicated capability namespace for devtools-specific browser launch settings in the WebdriverIO devtools package. Ensure backward compatibility while prioritizing the new namespace options over vendor-specific settings. Correct a naming inconsistency in a command descriptor field.

*   Update the `launch` function in `packages/devtools/src/launcher.ts`:
    *   Accept a `wdio:devtoolsOptions` capability key with an object value supporting:
        *   `headless` (boolean)
        *   `ignoreDefaultArgs` (boolean or string array)
        *   `defaultViewport` ({ width: number, height: number })
    *   Apply `wdio:devtoolsOptions` with highest priority, overriding vendor-specific options.
    *   Maintain backward compatibility:
        *   Respect `ignoreDefaultArgs` at the top level of capabilities.
        *   Allow `headless` and `defaultViewport` in vendor-specific namespaces.

*   Modify `DevToolsOptions` interface in `packages/devtools/src/launcher.ts`:
    *   Expand `ignoreDefaultArgs` to accept `boolean | string[]`.
    *   Add `defaultViewport?: { width: number, height: number }`.

*   Ensure backward compatibility for Chrome and Edge:
    *   Chrome: When `ignoreDefaultArgs` is an array at the top level and `headless: true` in `goog:chromeOptions`, pass `{ chromeFlags: [..., '--headless', ...], chromePath: undefined, ignoreDefaultFlags: true }` to chrome-launcher.
    *   Edge: When `ignoreDefaultArgs: true` at the top level and `headless: true` in `ms:edgeOptions`, pass `{ defaultViewport: { height: 900, width: 1200 }, executablePath: '/path/to/edge', headless: true, ignoreDefaultArgs: true, product: 'chrome' }` to puppeteer.

*   Update the `validate` function in `packages/devtools/src/utils.ts`:
    *   Rename the 4th parameter to `ref` and ensure all call sites use `ref` instead of `refs`.

*   Modify the `DevToolsDriver` class in `packages/devtools/src/devtoolsdriver.ts`:
    *   Ensure `register()` method includes a `ref` field in the command descriptor.
    *   Update TypeScript type to include `ref: string`.
    *   Allow `setTimeouts()` method to accept optional parameters (`implicit`, `pageLoad`, `script`). Ensure it can be called with no arguments.

*   Update the `DevTools` class in `packages/devtools/src/index.ts`:
    *   Ensure `newSession()` method accepts `modifier` and `customCommandWrapper` as optional parameters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.