Reorganize the ACL plugin permission manifest type into its own module and enhance the permission-processing function to support filtering. Ensure the manifest type is easily importable and that the function allows selective inclusion of permissions.

*   Move the `Manifest` struct:
    *   Relocate `Manifest` to `core/tauri-utils/src/acl/manifest.rs`.
    *   Ensure `Manifest` is importable as `tauri_utils::acl::manifest::Manifest`.
    *   Remove `Manifest` as the primary export from `tauri_utils::acl::plugin::Manifest`.
    *   Move `PermissionFile` to the new `manifest.rs` module.

*   Update the `define_permissions` function:
    *   Modify the function signature to `define_permissions<F: Fn(&Path) -> bool>(pattern: &str, pkg_name: &str, out_dir: &Path, filter_fn: F) -> Result<Vec<PermissionFile>, Error>`.
    *   Add a `filter_fn` parameter that accepts a closure, which takes a `&Path` and returns a `bool`.
    *   Ensure the function includes all permissions when `filter_fn` is `|_| true`, maintaining previous behavior.

*   Maintain existing functionality:
    *   Ensure `Manifest::new(permission_files, default_permission)` continues to function correctly after the module reorganization.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.