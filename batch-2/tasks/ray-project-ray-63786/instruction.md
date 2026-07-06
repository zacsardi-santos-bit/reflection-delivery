I'm working on the runtime environment packaging system and realized that the zip extraction logic has no protection against path traversal attacks.

*   The unzip_package function must skip any zip entry whose resolved path falls outside the target_dir, including entries using simple path traversal (e.g., '../outside.txt'), nested path traversal (e.g., 'dir/../../outside.txt'), and absolute paths (Unix-style '/absolute/file.txt' and Windows-style 'C:/absolute/file.txt', 'C:\\absolute\\file.txt'). Skipped entries must not create or modify any files outside target_dir.

*   The unzip_package function must still extract safe zip entries (those whose resolved paths remain within target_dir) to their correct locations under target_dir.

*   When remove_top_level_directory=True and all zip entries are unsafe (path traversal or absolute paths), no files must be extracted to target_dir. The external files referenced by unsafe entries must remain unchanged.

*   The unzip_package function must use os.path.realpath for containment validation but must write extracted files to the original extraction path (constructed from the original target_dir), not to any path derived from the realpath-resolved target directory.

*   The packaging module must access os.path.realpath through a module-level 'import os' so that the realpath function can be replaced via the module's 'os' attribute for testing purposes.

*   The remove_dir_from_filepaths function must raise ValueError when the rdir argument is an empty string, '.', '..', or any absolute path — including Unix-style absolute paths (starting with '/') and Windows-style absolute paths (e.g., 'C:/...' or 'C:\\...').


*   Interface details: Type: Function
Name: unzip_package
Location: python/ray/_private/runtime_env/packaging.py
Signature: unzip_package(package_path: str, target_dir: str, remove_top_level_directory: bool, unlink_zip: bool) -> None
Description: Extracts a zip archive to target_dir. Must skip any zip entry whose resolved path falls outside target_dir (path traversal or absolute paths). Must use os.path.realpath for containment validation but write files to the original extraction path. Must be imported by name into the test file.

Type: Function
Name: remove_dir_from_filepaths
Location: python/ray/_private/runtime_env/packaging.py
Signature: remove_dir_from_filepaths(base_dir: str, rdir: str) -> ...
Description: Strips a leading directory component from file paths. Must raise ValueError when rdir is an empty string, ".", "..", or any absolute path (Unix-style starting with "/" or Windows-style such as "C:/..." or "C:\\...").

Type: Module
Name: packaging_module
Location: python/ray/_private/runtime_env/packaging.py
Description: The packaging module itself, imported as a module-level reference in the test file (as `packaging_module`). The module must use `import os` at module level (not `from os.path import realpath`) so that `packaging_module.os.path.realpath` is accessible and can be patched for testing. The unzip_package function must call os.path.realpath through this module-level `os` reference.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.