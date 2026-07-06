Fix the disk usage reporting utility to correctly display the number of blocks available to unprivileged users in the "Avail" column. Ensure that the value reflects the user-available blocks (bavail) instead of the total free blocks (bfree).

*   Update the `From<Filesystem> for Row` implementation in `src/uu/df/src/table.rs`:
    *   Remove the `cfg(target_os = "macos")` guard from the bavail line to ensure bavail is used on all platforms.
    *   Compute the available bytes as `blocksize * bavail` instead of `blocksize * bfree`.
    *   Ensure the `bavail` field from `FsUsage` is used unconditionally.

*   Modify the `Display` implementation for `DisplayRow` in `src/uu/df/src/table.rs`:
    *   Ensure the `Column::Avail` variant uses the available-bytes field derived from `bavail` for rendering.
    *   Rename the field in `Row` from `bytes_free` to `bytes_avail` to reflect that it holds `bavail`-derived data.
    *   Update all struct-literal usages across the file, including tests, to match the new field name.

*   Ensure consistency in the `Row` struct:
    *   The `bytes_avail` field must accumulate and initialize the `bavail`-derived value.
    *   Update the `Row` initialization inside `AddAssign`, the default/empty constructor, and all relevant struct literals to use `bytes_avail`.

*   Verify the formatted output for a filesystem with `blocksize=1`, `bfree=750`, `bavail=600`, `blocks=1000`:
    *   Ensure the `DisplayRow` output contains `'         600'` (twelve characters wide, right-aligned) in the available column.

*   Ensure all existing display tests that construct `Row` struct literals continue to compile and pass.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.