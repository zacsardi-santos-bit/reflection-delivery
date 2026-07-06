Enhance the Keras model saving and loading system to address security vulnerabilities and correctness bugs. Implement checks to prevent maliciously crafted files from causing harm, such as arbitrary file reads, memory exhaustion, or execution of arbitrary code.

*   Implement error handling for HDF5 weight files:
    *   Raise `ValueError` with 'ExternalLink' when an HDF5 weight file contains an ExternalLink.
    *   Raise `ValueError` with 'SoftLink' when an HDF5 weight file contains a SoftLink.
    *   Raise `ValueError` with 'virtual' when an HDF5 weight file contains a virtual dataset.
    *   Use `safe_get_h5_dataset(f, name)` to raise `ValueError` with 'shape bomb' for datasets with excessively large shapes.

*   Implement decompression bomb protection:
    *   Define `_safe_zip_read(zf, name)` to raise `ValueError` with 'decompression bomb' when the uncompressed size to compressed size ratio exceeds `_ZIP_MEMBER_MAX_EXPANSION` for members larger than `_ZIP_MEMBER_BOMB_FLOOR_BYTES`.
    *   Ensure `load_model(path)` uses `_safe_zip_read` for reading `.keras` zip archives.

*   Secure asset directory operations:
    *   In `DiskIOStore`, ensure `make(path)` and `get(path)` raise `ValueError` with 'Invalid asset path' for paths containing traversal sequences.
    *   Ensure `has_path(path)` returns `False` for traversal paths.
    *   Ensure `_full_path(path)` raises `ValueError` for traversal paths and preserves remote prefixes for remote directories.

*   Implement safe deserialization:
    *   Introduce `SafeModeScope` as a context manager to control deserialization safety, allowing unsafe operations only when `safe_mode=False`.
    *   Ensure deserialization of Lambda layer configs and TorchModuleWrapper configs raises `ValueError` with appropriate messages unless explicitly allowed.

*   Correct sharded weight file reading:
    *   Ensure `ShardedH5IOStore.get(name)` retrieves layer data correctly, regardless of the order of access.

*   Secure tar archive extraction:
    *   Implement `filter_safe_tarinfos(members, base_dir)` to reject tar members with paths routed through in-bounds symlinks using '..'.
    *   Ensure `is_link_in_dir` checks both tarinfo name and linkname for directory traversal.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.