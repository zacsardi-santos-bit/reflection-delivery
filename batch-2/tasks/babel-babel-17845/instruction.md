I'm working with Babel's React preset and JSX development transform.

*   The `normalizeOptions` function must recognize `developmentSourceSelf` as a valid top-level option of boolean type.

*   When `developmentSourceSelf` is provided as a non-boolean value (e.g., the number 0), `normalizeOptions` must throw with the message: "@babel/preset-react: 'developmentSourceSelf' option must be a boolean."

*   The `normalizeOptions` function must default `developmentSourceSelf` to `false` when not specified. The returned object must include a `developmentSourceSelf` property with value `false` for both `normalizeOptions({})` and `normalizeOptions({ runtime: 'classic' })`.

*   The `createPlugin` factory function must accept a `developmentSourceSelf` boolean parameter (defaults to `false`). This function must be accessible as the default export of `@babel/plugin-transform-react-jsx/lib/create-plugin`.

*   When `createPlugin` is called with `developmentSourceSelf: true`, the resulting plugin must include source location information (fileName, lineNumber, columnNumber) and the current context (`this`) as trailing arguments in `jsxDEV` calls during development-mode JSX transformation.

*   When `createPlugin` is called with `developmentSourceSelf: false` (or omitted), the resulting plugin must produce `jsxDEV` calls with exactly 4 arguments — component, props, key, and isStaticChildren — with no source location info and no `this` argument.

*   The React preset with `development: true` but without `developmentSourceSelf: true` must output `jsxDEV` calls without source location or context arguments. There must be no `_jsxFileName` variable declaration in the output.

*   The default `transform-react-jsx-development` plugin (used without a `developmentSourceSelf: true` override) must transform JSX to `jsxDEV` calls without source location info (no fileName, lineNumber, columnNumber, or `this` arguments).

*   Existing test fixtures that previously relied on source location info being included automatically must now pass `developmentSourceSelf: true` explicitly to preserve that behavior.


*   Interface details: Type: Function
Name: normalizeOptions
Location: packages/babel-preset-react/src/normalize-options.ts
Signature: normalizeOptions(options?: any) -> { development: boolean | undefined, developmentSourceSelf: boolean, importSource: string | undefined, pragma: string | undefined, pragmaFrag: string | undefined, pure: boolean | undefined, runtime: string | undefined, throwIfNamespace: boolean }
Description: Validates and normalizes options for the React preset. Must accept `developmentSourceSelf` as a valid boolean option with a default value of `false`. When `developmentSourceSelf` is provided as a non-boolean, must throw the error: "@babel/preset-react: 'developmentSourceSelf' option must be a boolean."

Type: Function
Name: createPlugin
Location: packages/babel-plugin-transform-react-jsx/src/create-plugin.ts
Signature: createPlugin({ name: string, development: boolean, developmentSourceSelf?: boolean }) -> BabelPlugin
Description: Factory that creates the React JSX transform plugin. Must accept a `developmentSourceSelf` boolean parameter (defaults to `false`). When `developmentSourceSelf` is `true`, development-mode JSX transforms include source location info (fileName, lineNumber, columnNumber) and `this` as trailing arguments to jsxDEV calls. When `developmentSourceSelf` is `false` (default), jsxDEV calls are generated with only 4 arguments (component, props, key, isStaticChildren) — no source location or context. This function must be exported as the default export from `@babel/plugin-transform-react-jsx/lib/create-plugin`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.