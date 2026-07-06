Implement the `useSlashCompletion` function to enhance the slash command autocomplete feature in the CLI tool. Ensure it supports case-insensitive and typo-tolerant fuzzy matching using the `AsyncFzf` class from the 'fzf' library. Handle errors gracefully by falling back to prefix-based matching and log any errors encountered.

*   Update the `useSlashCompletion` function in `packages/cli/src/ui/hooks/useSlashCompletion.ts` to:
    *   Use `AsyncFzf` from the 'fzf' library for fuzzy matching.
    *   Import `AsyncFzf` directly from 'fzf' to allow test mocking via `vi.mock('fzf')`.
    *   Perform case-insensitive fuzzy matching, ensuring queries like '/HeLp' return the command 'help'.
    *   Implement typo-tolerant (subsequence-based) matching, allowing queries like '/hlp' to match 'help'.
    *   Cache the `AsyncFzf` instance for reuse across re-renders when the command list reference remains unchanged.
    *   Ensure the `AsyncFzf` constructor is not called again for the same command list in consecutive renders.

*   Implement error handling:
    *   If `AsyncFzf.find()` throws an error, fall back to prefix-based matching.
    *   Perform prefix-based matching case-insensitively on command names and their alternative names.
    *   Log errors using `console.error` with the exact format: `console.error('[Fuzzy search - falling back to prefix matching]', <Error object>)`.

*   Ensure the suggestions list:
    *   Contains each command only once, even if a query matches both its primary and alternative names.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.