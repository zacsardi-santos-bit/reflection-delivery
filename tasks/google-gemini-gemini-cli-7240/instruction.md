Implement a file-based token storage system for OAuth credentials that encrypts and saves tokens to disk, allowing for persistence across CLI restarts. Ensure security by using machine- and user-specific encryption keys and handle errors clearly.

*   Implement the `FileTokenStorage` class in `packages/core/src/mcp/token-storage/file-token-storage.ts`.
    *   Extend `BaseTokenStorage` and require a `serviceName` string in the constructor.
    *   Store credentials as an encrypted JSON file at `path.join(os.homedir(), '.gemini', 'mcp-oauth-tokens-v2.json')`.
    *   Use a private `encrypt` method to return strings in the format '<iv_hex>:<authTag_hex>:<encrypted_hex>'.
        *   Ensure each encryption uses a random initialization vector.
    *   Use a private `decrypt` method that throws 'Invalid encrypted data format' for improperly formatted input.
*   Implement `getCredentials(serverName: string): Promise<OAuthCredentials | null>`.
    *   Throw 'Token file does not exist' if the file is absent.
    *   Return `null` if the token's `expiresAt` timestamp is in the past.
    *   Return the full `OAuthCredentials` object if the token is valid.
    *   Throw 'Token file corrupted' if decryption or parsing fails.
*   Implement `setCredentials(credentials: OAuthCredentials): Promise<void>`.
    *   Create the `~/.gemini` directory with `{ recursive: true, mode: 0o700 }`.
    *   Write the encrypted credentials file with `{ mode: 0o600 }`.
    *   Ensure the data is in the three-part hex format.
    *   Merge new credentials with existing ones.
*   Implement `deleteCredentials(serverName: string): Promise<void>`.
    *   Throw 'Token file does not exist' if the file is absent.
    *   Delete the file if the last credential is removed.
    *   Otherwise, update the file by removing the specified server's entry.
*   Implement `listServers(): Promise<string[]>`.
    *   Throw 'Token file does not exist' if the file is absent.
    *   Return an array of server names for all stored credentials.
*   Implement `clearAll(): Promise<void>`.
    *   Delete the token file and ignore ENOENT errors silently.
*   Update `BaseTokenStorage` in `packages/core/src/mcp/token-storage/base-token-storage.ts`.
    *   Require the `serviceName` parameter with no default value in the constructor.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.