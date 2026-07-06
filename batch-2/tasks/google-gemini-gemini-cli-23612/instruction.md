Implement a file system service that proxies read and write operations to a connected agent session, ensuring operations are only forwarded when the file path is within a specified root directory and not within a user's configuration folder. Translate specific remote errors into standard filesystem errors.

*   Update the `AcpFileSystemService` class in `packages/cli/src/acp/fileSystemService.ts`:
    *   Modify the constructor to accept a fifth parameter for the root directory path:
        *   Signature: `constructor(connection: AgentSideConnection, sessionId: string, capabilities: { readTextFile: boolean; writeTextFile: boolean }, fallback: FileSystemService, root: string)`
    *   Implement path-based access control in `readTextFile` and `writeTextFile` methods:
        *   Route operations to the remote connection only if:
            *   The respective capability (`readTextFile` or `writeTextFile`) is enabled.
            *   The file path is within the specified root directory and not inside the user's home-directory-based tool configuration folder (`os.homedir() + '/.gemini'`).
        *   Fall back to the local file system service if the above conditions are not met.
    *   Ensure files inside the global tool configuration directory (`os.homedir() + '/.gemini/...'`) are always handled by the local fallback, regardless of root directory overlap.
*   Error handling:
    *   For `readTextFile` and `writeTextFile` methods, if the remote connection throws an error with a message containing 'Resource not found':
        *   Reject with an error object having:
            *   Code: `'ENOENT'`
            *   Message: The original error message (e.g., 'Resource not found for document' or 'Resource not found for directory').

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.