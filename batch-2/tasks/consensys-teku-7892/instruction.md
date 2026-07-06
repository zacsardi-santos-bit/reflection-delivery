Refactor the deposit snapshot loading system to support multiple ordered snapshot sources with configurable priority levels and error handling. Consolidate the configuration settings into a single object to improve manageability and flexibility.

*   Implement `DepositSnapshotFileLoader`:
    *   Expose a static inner `Builder` class with methods:
        *   `addRequiredResource(String path) -> Builder`
        *   `addOptionalResource(String path) -> Builder`
        *   `build() -> DepositSnapshotFileLoader`
    *   Replace the old constructor with the `Builder` pattern.
    *   Expose a static inner class or record `DepositSnapshotResource` with:
        *   A constructor `DepositSnapshotResource(String path, boolean required)`
        *   Support for equality comparison (`equals` and `hashCode`).
    *   Provide `getDepositSnapshotResources() -> List<DepositSnapshotResource>` to return configured resources.
    *   Ensure `loadDepositSnapshot()` returns a `LoadDepositSnapshotResult` with an empty `getDepositTreeSnapshot()` when built with no resources.
    *   Ensure resources are tried in order:
        *   Skip optional resources if not found.
        *   Throw `InvalidConfigurationException` with 'File \''<path>\'' not found' for missing required resources.
        *   Stop processing after the first failed required resource.

*   Create `DepositTreeSnapshotConfiguration` in `services/powchain` module:
    *   Methods:
        *   `getCustomDepositSnapshotPath() -> Optional<String>`
        *   `getBundledDepositSnapshotPath() -> Optional<String>`
        *   `isBundledDepositSnapshotEnabled() -> boolean`
        *   `getCheckpointSyncDepositSnapshotUrl() -> Optional<String>`

*   Update `PowchainConfiguration`:
    *   Add `getDepositTreeSnapshotConfiguration() -> DepositTreeSnapshotConfiguration`.
    *   Ensure `getCustomDepositSnapshotPath()` and `getBundledDepositSnapshotPath()` return correct paths based on configuration.
    *   Ensure `isBundledDepositSnapshotEnabled()` returns false when `depositSnapshotEnabled(false)` is set.
    *   Define `DEPOSIT_SNAPSHOT_URL_PATH` as a public static String constant.

*   Implement CLI flag handling:
    *   When `--Xdeposit-snapshot` is used, disable the bundled path and prioritize the custom path.
    *   When `--checkpoint-sync-url` is set, derive the deposit snapshot URL using `DEPOSIT_SNAPSHOT_URL_PATH`.

*   Update `PowchainService`:
    *   Expose `getEth1DepositManager() -> Eth1DepositManager`.
    *   Ensure `PowchainService` constructs `DepositSnapshotFileLoader` with resources in the specified priority order.

*   Update `Eth1DepositManager`:
    *   Expose `getDepositSnapshotFileLoader() -> DepositSnapshotFileLoader`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.