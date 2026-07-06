Implement a structured configuration for snapshot storage in the qdrant project. Replace the existing implicit storage configuration with an explicit, extensible structure that clearly defines the storage backend and nests cloud credentials appropriately. Ensure the default configuration maintains local filesystem storage behavior.

*   Update `StorageConfig` in `lib/storage/src/types.rs`:
    *   Replace `s3_config: Option<S3Config>` with `snapshots_config` implementing `Default`.
    *   Ensure all code referencing `s3_config` is updated to use `snapshots_config`.

*   Modify `SharedStorageConfig` in `lib/collection/src/operations/shared_storage_config.rs`:
    *   Replace `s3_config: Option<S3Config>` with `snapshots_config: SnapShotsConfig`.
    *   Update the `Default` implementation and constructor to reflect this change.

*   Define or update `SnapShotsConfig` in `lib/collection/src/common/snapshots_manager.rs`:
    *   Derive `Default`, `Clone`, `Deserialize`, and `Debug`.
    *   Include a `snapshots_storage` field of type `SnapshotsStorageConfig` with `Local` as the default variant and `S3` as an alternative.
    *   Include an `s3_config: Option<S3Config>` field for cloud credentials.

*   Update `S3Config` in `lib/collection/src/common/snapshots_manager.rs`:
    *   Add `endpoint_url: Option<String>` to support S3-compatible storage endpoints.
    *   Ensure the struct derives `Default`, `Clone`, `Deserialize`, and `Debug`.

*   Adjust application YAML configuration:
    *   Use `storage.snapshots_config` as the key for snapshot storage backend configuration.
    *   Under `storage.snapshots_config`, `snapshots_storage` must accept `"local"` or `"s3"` as values.
    *   When `snapshots_storage` is `"s3"`, nest S3 credentials under `storage.snapshots_config.s3_config` with keys: `bucket`, `region`, `access_key`, `secret_key`, and `endpoint_url`.

*   Ensure `snapshots_config` defaults to local filesystem storage, equivalent to the old `s3_config: None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.