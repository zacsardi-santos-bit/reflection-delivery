Refactor the file system module to improve consistency and usability. Implement changes to the file system class and file objects to address API inconsistencies and enhance functionality.

*   Update the `FileSystem` class:
    *   Modify the constructor to accept `base_dir` as either a string or a `Path` object, and add a `create_default_files` boolean parameter (default `True`).
    *   Store `base_dir` as a `Path` object in a `base_dir` attribute and expose a `data_dir` attribute whose name equals `DEFAULT_FILE_SYSTEM_PATH`.
    *   Implement a `nuke()` method to delete the `data_dir` directory from disk.
    *   Implement a `get_allowed_extensions()` method returning a collection of 'md' and 'txt'.
    *   Ensure `read_file(filename)` is synchronous and returns specific messages for success, file not found, and invalid filenames.
    *   Implement `write_file(filename, content)` and `append_file(filename, content)` with specific return messages for success and invalid filenames.
    *   Implement `save_extracted_content(content)` to save content and return a success message with the current count.
    *   Ensure `get_state()` returns a `FileSystemState` with the correct `base_dir` and file entries.
    *   Ensure `from_state(state)` skips unrecognized file types and matches the restored `base_dir` with the state.
    *   Implement `_parse_filename(filename)` to lowercase file extensions.
    *   Implement `_is_valid_filename(filename)` to reject filenames with multiple extension separators, special characters, and empty base names.
    *   Implement `describe()` to show both the beginning and end of large files with a 'more lines' indicator.
    *   Implement `get_dir()` to return the `data_dir` as a `Path` object.

*   Update `MarkdownFile` and `TxtFile` classes:
    *   Expose `get_size` and `get_line_count` as properties.
    *   Implement `update_content(new_content: str)` to replace in-memory content.
    *   Implement `async sync_to_disk(base_dir: Path)`, `sync_to_disk_sync(base_dir: Path)`, `async write(content: str, base_dir: Path)`, and `async append(content: str, base_dir: Path)` methods for disk operations.

*   Export constants:
    *   `DEFAULT_FILE_SYSTEM_PATH` and `INVALID_FILENAME_ERROR_MESSAGE` must be importable from `browser_use/filesystem/file_system.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.