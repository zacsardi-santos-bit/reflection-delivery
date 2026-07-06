Ensure the `FileSystemRealPathCache` type is publicly accessible from the root of the `parcel_filesystem` crate. Implement the necessary changes to allow this type to be instantiated using the default construction pattern.

*   Export the `FileSystemRealPathCache` type from the root module of the `parcel_filesystem` crate.
    *   Ensure it is accessible as `crate::FileSystemRealPathCache`.
*   Implement the `Default` trait for `FileSystemRealPathCache`.
    *   Allow instances to be created using `FileSystemRealPathCache::default()`.
*   Make the necessary changes in `crates/parcel_filesystem/src/lib.rs` to declare the public export.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.