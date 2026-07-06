Implement a secure logging utility to record RAG trace data to a local file. Ensure the logger can be initialized with a directory path and append structured trace entries as JSON lines to a log file. Maintain strict owner-only permissions for both the directory and file to protect sensitive data.

*   Create the `RagLogger` class in `packages/core/src/utils/ragLogger.ts`.
    *   Export `RagLogger` as a named class export.
*   Implement the `initialize(logsDir: string): void` method.
    *   Create the directory using `fs.mkdirSync(logsDir, { recursive: true, mode: 0o700 })`.
    *   Resolve the real path with `fs.realpathSync(logsDir)`.
    *   Apply permissions using `fs.chmodSync(realPath, 0o700)`.
    *   If directory creation fails, catch the error and call `debugLogger.error` with 'Failed to create or set permissions for rag-trace.log directory' and the error.
*   Implement the `log(entry: { sessionId: string; ragStatus: string; snippets: Array<{ content: string; relevanceScore?: number }> }): void` method.
    *   Check if `initialize()` was called; if not, call `debugLogger.warn` with 'RagLogger was called before being initialized.' and return.
    *   Open the log file with `fs.openSync(filePath, 'a', 0o600)`.
    *   On the first write after initialization, enforce permissions with `fs.fchmodSync(fd, 0o600)`.
    *   Write the entry as JSON with a timestamp using `fs.writeSync(fd, JSON.stringify({ timestamp: new Date().toISOString(), ...entry }) + '\n', null, 'utf8')`.
    *   Close the file descriptor with `fs.closeSync(fd)`.
    *   If any file operation fails, catch the error and call `debugLogger.error` with `Failed to write to ${path.join(logsDir, 'rag-trace.log')}` and the error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.