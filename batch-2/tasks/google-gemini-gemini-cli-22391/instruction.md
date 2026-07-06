Implement a fallback mechanism for credential storage in environments where the native OS keychain is unavailable. Ensure the service remains functional by using an encrypted file-based storage backend when necessary, and simplify the storage coordination logic.

*   Create a `FileKeychain` class in `packages/core/src/services/fileKeychain.ts`:
    *   Implement the `Keychain` interface.
    *   Provide a constructor with no arguments.
    *   Implement methods: `getPassword`, `setPassword`, `deletePassword`, `findCredentials`.

*   Update `KeychainService`:
    *   Modify `isAvailable()` to always return `true`.
    *   Instantiate `FileKeychain` when the native keychain is unavailable or when `GEMINI_FORCE_FILE_STORAGE` is set to 'true'.
    *   Log a debug message containing 'Using FileKeychain fallback' when using the fallback.
    *   Emit telemetry with `available: false` when using the fallback.
    *   Implement `isUsingFileFallback()` method returning `Promise<boolean>` to indicate if `FileKeychain` is active.
    *   Ensure password operations (`getPassword`, `setPassword`, `deletePassword`, `findCredentials`) function correctly via `FileKeychain`.

*   Update `KeychainTokenStorage`:
    *   Implement `isUsingFileFallback()` method returning `Promise<boolean>`, delegating to `KeychainService.isUsingFileFallback()`.

*   Update `HybridTokenStorage`:
    *   Use `KeychainTokenStorage.isUsingFileFallback()` to determine storage type.
    *   Return `TokenStorageType.KEYCHAIN` from `getStorageType()` when `isUsingFileFallback()` is `false`.
    *   Return `TokenStorageType.ENCRYPTED_FILE` from `getStorageType()` when `isUsingFileFallback()` is `true`.
    *   Remove direct dependency on `FileTokenStorage` and route operations through `KeychainTokenStorage`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.