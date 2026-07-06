Implement a cryptographic integrity check for the extension management system to prevent unauthorized modifications to extension metadata. Ensure secure storage of integrity data and improve error handling and logging for broken extensions. Enhance the system's behavior on macOS regarding keychain availability.

*   Implement the `ExtensionIntegrityManager` class in `packages/core/src/config/extensions/integrity.ts`.
    *   Accept no constructor arguments.
    *   Expose methods: `getSecretKey()`, `store(extensionName, metadata)`, and `verify(extensionName, metadata)`.
    *   `getSecretKey()` must retrieve or generate a 64-character key, using the keychain or a file fallback.
    *   `store()` must save a cryptographic signature of metadata, handling errors for compromised or invalid JSON.
    *   `verify()` must return `IntegrityDataStatus.VERIFIED`, `MISSING`, or `INVALID` based on signature checks.

*   Define `IntegrityDataStatus` in `packages/core/src/config/extensions/integrity.ts` with values: `VERIFIED`, `MISSING`, `INVALID`.
    *   Re-export `ExtensionIntegrityManager` and `IntegrityDataStatus` from the core package's public index.

*   Update `ExtensionManager` in `packages/cli/src/config/extension-manager.ts`.
    *   Accept an `integrityManager` parameter in the constructor.
    *   Expose methods: `storeExtensionIntegrity()` and `verifyExtensionIntegrity()`.
    *   Ensure `loadExtension()` is public.
    *   During `loadExtensions()`, detect and delete orphaned linked extensions.
    *   Log warnings for broken extensions via `console.warn` with a consistent message format.

*   Modify `updateExtension` in `packages/cli/src/config/extensions/update.ts`.
    *   Verify integrity before updates using `verifyExtensionIntegrity()`.
    *   Handle `IntegrityDataStatus.INVALID` by throwing an error and dispatching a state update.
    *   Allow updates for `IntegrityDataStatus.MISSING` and handle errors from `verifyExtensionIntegrity()`.

*   Enhance `KeychainService` in `packages/core/src/services/keychainService.ts` for macOS.
    *   Use `spawnSync` to check for a valid default keychain.
    *   Fall back to `FileKeychain` if the keychain is missing or the path doesn't exist.
    *   Return true from `isAvailable()` in all cases, ensuring fallback availability.

*   Ensure the extension update CLI command prompts for user confirmation before proceeding.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.