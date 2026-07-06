Implement Azure container name validation in the SkyPilot storage system to ensure consistent behavior across all supported cloud storage backends. Add validation logic to reject invalid Azure container names before any network calls are made, providing users with clear and actionable error messages.

*   Update the `StoreType` enum:
    *   Include an `AZURE` member to identify Azure Blob Storage as a supported store type.

*   Implement the `AzureBlobStore` class in `sky/data/storage.py`:
    *   Extend the existing `AbstractStore` base class.
    *   Register as the store class for the `StoreType.AZURE` enum value.

*   Implement the `validate_name` classmethod in `AzureBlobStore`:
    *   Signature: `validate_name(cls, name: str) -> str`
    *   Validate Azure container names according to the following rules:
        *   Raise `exceptions.StorageNameError` if the name is shorter than 3 characters or longer than 63 characters.
        *   Raise `exceptions.StorageNameError` if the name contains any uppercase letters.
        *   Raise `exceptions.StorageNameError` if the name does not start with a lowercase letter or digit.
        *   Raise `exceptions.StorageNameError` if the name contains two consecutive hyphens.
        *   Raise `exceptions.StorageNameError` if the name is `None` or not a string.
    *   Return the name if it passes all validations.

*   Ensure existing S3 and GCS name validation behavior remains unchanged:
    *   Invalid S3 and GCS bucket names must continue to raise `StorageNameError` as before.

*   Define the class attribute `AzureBlobStore.DEFAULT_STORAGE_ACCOUNT_NAME`:
    *   Location: `sky/data/storage.py`
    *   Description: Class-level string constant providing the default naming template for Azure storage accounts.
    *   Format: Use `{region}` and `{user_hash}` as placeholders.
    *   Value: `'sky{region}{user_hash}'`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.