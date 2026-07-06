Implement a configurable socket server in webpack-dev-server that allows users to specify their desired implementation. Provide flexibility by accepting a short string identifier, a full file path, or a class directly. Ensure the system defaults to the existing implementation when no custom option is provided.

*   Create a utility function `getSocketServerImplementation` in `lib/utils/getSocketServerImplementation.js`.
    *   Accept an `options` object with a `serverMode` property.
    *   Return the SockJSServer class when `options.serverMode` is 'sockjs'.
    *   Return the class or constructor function directly if `options.serverMode` is a class.
    *   Load and return a module when `options.serverMode` is a valid absolute path.
    *   Throw an Error with a message matching /serverMode must be a string/ if the path is invalid.

*   Update the dev server to support a `serverMode` configuration option.
    *   Ensure the server starts successfully with 'sockjs', a valid module path, or a class.
    *   Instantiate and use a custom class extending `BaseServer` when provided.
    *   Throw an error with a message matching /serverMode must be a string/ if the `serverMode` cannot be resolved.

*   Modify `lib/options.json` to include the `serverMode` option.
    *   Allow strings and Functions as valid values.
    *   Disallow empty strings and booleans.

*   Update `lib/Server.js` to utilize `getSocketServerImplementation`.
    *   Replace hardcoded socket server references with resolved classes from `options.serverMode`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.