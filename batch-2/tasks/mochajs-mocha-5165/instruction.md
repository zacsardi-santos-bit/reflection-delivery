Upgrade the argument parsing library used by Mocha's CLI to the newer major version. Update the import pattern and remove deprecated methods to ensure compatibility with the new version while preserving all existing CLI options.

*   Upgrade the argument parsing library dependency in the project manifest from version 16 to version 17.
    *   Update the `yargs-parser` dependency accordingly.
*   Modify the import statement in `lib/cli/cli.js`:
    *   Change from the old subpath export pattern (`require('yargs/yargs')`) to the modern top-level import pattern (`require('yargs')`).
*   Update the builder function in `lib/cli/run.js`:
    *   Ensure compatibility with the argument parsing library's v17 API.
    *   Use the library's default export as a factory function to create a fresh instance.
*   Register CLI options with a fresh yargs instance:
    *   Number-type options: `retries`, `jobs`.
    *   String-type options: `config`, `fgrep`, `grep`, `package`, `reporter`, `ui`, `slow`, `timeout`.
    *   Boolean-type options: `allow-uncaught`, `async-only`, `bail`, `check-leaks`, `color`, `delay`, `diff`, `dry-run`, `exit`, `pass-on-failing-test-suite`, `fail-zero`, `forbid-only`, `forbid-pending`, `full-trace`, `inline-diffs`, `invert`, `list-interfaces`, `list-reporters`, `no-colors`, `parallel`, `recursive`, `sort`, `watch`.
    *   Array-type options: `extension`, `file`, `global`, `ignore`, `node-option`, `reporter-option`, `require`, `spec`, `watch-files`, `watch-ignore`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.