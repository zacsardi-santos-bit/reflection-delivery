Fix the runtime configuration reload logic for pinned images to ensure that when an empty list is provided, all previously pinned images are unpinned. Implement the necessary changes in the `ReloadPinnedImages` method to achieve the expected behavior.

*   Update the `ReloadPinnedImages` method in `pkg/config/reload.go`:
    *   Ensure the method signature is `ReloadPinnedImages(newConfig *Config)`.
    *   Set the receiver's `PinnedImages` field to an empty slice when `newConfig.PinnedImages` is an empty slice.
    *   Handle cases where the receiver currently has pinned images, ensuring the `PinnedImages` field becomes an empty slice when `newConfig.PinnedImages` is empty.
*   Verify that the `PinnedImages` field in the `Config` struct:
    *   Is located in the `pkg/config` package.
    *   Is a public field with the signature `PinnedImages []string`.
    *   Is writable and readable, allowing updates to reflect the new configuration state.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.