Implement a new function named `assemble` in the `Directories` struct within `src/docker/shared.rs` to replace the existing `create` function. Update the function signature to take ownership of the metadata and return both the assembled directories and the updated metadata. Ensure all existing calls to the old function are updated to use the new function name and handle the new return type.

*   Implement the `assemble` function in the `Directories` struct:
    *   Replace the existing function named `create`.
    *   Accept the following parameters:
        *   `mount_finder`: a reference to a `MountFinder`.
        *   `metadata`: a `CargoMetadata` value (by ownership).
        *   `cwd`: a reference to a `Path` representing the current working directory.
        *   `toolchain`: a `QualifiedToolchain` value.
    *   Return a `Result<(Directories, CargoMetadata)>`:
        *   A tuple containing the assembled `Directories` and the (possibly updated) `CargoMetadata`.

*   Update all existing call sites:
    *   Replace calls to `Directories::create` with `Directories::assemble`.
    *   Ensure the new function signature is used, taking `CargoMetadata` by value.
    *   Handle the new return type, which includes both `Directories` and `CargoMetadata`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.