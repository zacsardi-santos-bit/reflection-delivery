Implement a pre-flight blocking check system for Storybook's CLI to detect and report environment compatibility issues before proceeding with upgrades or initialization. Develop a factory function to define individual blockers and a main function to execute these checks and handle the results.

*   Implement the `autoblock` function in `code/lib/cli/src/autoblock/index.ts` with the following behavior:
    *   Accept an `options` object with fields: `configDir`, `mainConfig`, `mainConfigPath`, `packageJson`, and `packageManager`.
    *   Accept an array of promises, each resolving to an object containing a `blocker` property.
    *   Return `null` without any output if the blockers array is empty.
    *   Return `null` and log 'No blockers found' if all blockers pass (check function returns `false`).
    *   Return the `id` of the first failing blocker if any blocker fails (check function returns a non-false object).
    *   Write a log file using `node:fs/promises` `writeFile` function, formatting each failing blocker as '(id):\n<log output>'.
    *   Separate multiple failing blocker entries in the log file with '\n\n----\n\n'.
    *   Log 'Oh no..' using `logger.plain` when any blocker fails.

*   Implement the `createBlocker` function in `code/lib/cli/src/autoblock/types.ts` with the following behavior:
    *   Accept a configuration object with fields: `id` (string), `check` (async function returning `false` or an object), `message` (function returning a string), and `log` (function returning a string).
    *   Return a blocker instance that can be passed to the `autoblock` function.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.