Fix the bundler to ensure that entry modules serving as dynamic import targets retain their exports in the output, even when entry signature preservation is disabled. Ensure that shared content across multiple entries is deduplicated into a single chunk.

*   Implement logic to handle `preserveEntrySignatures` set to `"false"`:
    *   Ensure entry modules that are also dynamic import targets retain their exports in the output chunk.
    *   Allow entry modules that are not dynamic import targets to have their exports suppressed and potentially moved to a shared helper chunk.
*   Deduplicate shared content:
    *   Extract shared content into a single deduplicated chunk when multiple entry points reference the same source module.
    *   Ensure each entry imports from the deduplicated chunk as needed.
*   Update the configuration:
    *   Accept the string value `"false"` for `preserveEntrySignatures` in `_config.json` to enable this bundling mode.
*   Ensure filename hash snapshots:
    *   Record deterministic content hashes for all output chunks in the test case `tests/rolldown/misc/preserve_entry_signature/issue-4880`, including dynamic, foo, foo2, main, and the deduplicated helper chunk (foo22).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.