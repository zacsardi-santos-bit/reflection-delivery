Implement a feature where the AI assistant automatically discovers and includes relevant project-level context during file operations. Update the system to refresh the context manager when a chat session is reset to prevent outdated context from being used.

*   Create a new module at `packages/core/src/tools/jit-context.ts` with the following exports:
    *   `discoverJitContext(config: Config, accessedPath: string): Promise<string>`: Discovers just-in-time context for a given path. Returns an empty string if JIT context is disabled, no context manager is available, nothing is found, or an error occurs.
    *   `appendJitContext(llmContent: string, jitContext: string): string`: Appends JIT context to content if non-empty, using `JIT_CONTEXT_PREFIX` and `JIT_CONTEXT_SUFFIX`.
    *   `JIT_CONTEXT_PREFIX`: `'\n\n--- Newly Discovered Project Context ---\n'`
    *   `JIT_CONTEXT_SUFFIX`: `'\n--- End Project Context ---'`

*   Implement `discoverJitContext` to:
    *   Return an empty string if `config.isJitContextEnabled()` is false, `config.getContextManager()` is undefined, or if context discovery returns an empty string or throws an error.
    *   Call `config.getContextManager().discoverContext(accessedPath, workspaceDirectories)` when JIT context is enabled and a context manager is available, where `workspaceDirectories` is obtained from `config.getWorkspaceContext().getDirectories()`.

*   Implement `appendJitContext` to:
    *   Return the original `llmContent` unchanged if `jitContext` is empty or falsy.
    *   Append `jitContext` to `llmContent` using the format `${llmContent}${JIT_CONTEXT_PREFIX}${jitContext}${JIT_CONTEXT_SUFFIX}` when `jitContext` is non-empty.

*   Update the following tools to call `discoverJitContext` and append results using `appendJitContext`:
    *   Edit/replace tool
    *   Write-file tool
    *   Read-file tool
    *   List-directory (ls) tool
    *   Read-many-files tool

*   Implement chat session reset logic:
    *   When `resetChat()` is called, refresh the context manager by calling `refresh()` if `config.getContextManager()` returns a context manager.
    *   Ensure `resetChat()` completes successfully without throwing if `config.getContextManager()` returns undefined.

*   Update the `Config` interface to include:
    *   `getContextManager(): ContextManager | undefined`: Returns the active `ContextManager` instance or undefined.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.