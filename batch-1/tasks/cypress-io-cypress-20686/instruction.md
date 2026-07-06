Update the Cypress installer to correctly detect and handle architecture on Apple Silicon Macs, ensuring the correct binary is downloaded. Implement architecture detection that accounts for Rosetta emulation and centralize the architecture parameter in URL construction functions.

*   Modify `getUrl` in `cli/lib/tasks/download.js`:
    *   Accept `arch` as the first parameter and `version` as an optional second parameter.
    *   Return the `version` if it is an HTTP or HTTPS URL.
    *   Construct and return a platform- and architecture-specific URL using the provided `arch`.

*   Update `start` function in `cli/lib/tasks/download.js`:
    *   Make it an async function.
    *   Call `util.getRealArch()` to determine the system architecture.
    *   Pass the `arch` value to `getUrl(arch, version)` when constructing the download URL.

*   Ensure download requests on macOS:
    *   Include `arch=arm64` and `platform=darwin` when `os.arch()` returns 'arm64'.
    *   Include `arch=arm64` when `os.arch()` returns 'x64' and `sysctl -n sysctl.proc_translated` outputs '1'.

*   Modify `_getBinaryUrlFromBuildInfo` in `cli/lib/tasks/install.js`:
    *   Accept `arch` as the first parameter and a `buildInfo` object as the second parameter.
    *   Generate a URL in the format: `https://cdn.cypress.io/beta/binary/{version}/{platform}-{arch}/{commitBranch}-{commitSha}/cypress.zip`.

*   Update `util` object in `cli/lib/util.js`:
    *   Add a `_cachedArch` property initialized to `undefined` to cache the architecture detected by `getRealArch()`.
    *   Make `_cachedArch` publicly accessible and resettable.

*   Implement `getRealArch` in `cli/lib/util.js`:
    *   Return 'arm64' on macOS if `os.arch()` is 'arm64'.
    *   If `os.arch()` is 'x64', check Rosetta translation using `sysctl -n sysctl.proc_translated`.
    *   Cache the result in `util._cachedArch`.

*   Create a local test support module at `cli/test/support/spawn-mock.js`:
    *   Export a `mockSpawn(cb)` function that wraps the `spawn-mock` npm package's `mockSpawn`.
    *   Add a `.cancel` sinon stub to each mock child process before invoking the callback.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.