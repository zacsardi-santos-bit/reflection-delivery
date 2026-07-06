Implement two new path utilities in the `streaming_download_manager` module to support glob-style pattern matching for both local paths and remote URLs. These utilities should enable file enumeration by pattern in streaming mode, similar to existing path operations.

*   Add the `xpathglob` function to `src/datasets/utils/streaming_download_manager.py`:
    *   Ensure it is importable from `datasets.utils.streaming_download_manager`.
    *   Accepts a `path` (Path object) and a `pattern` (string).
    *   Yields `Path` objects matching the pattern within the given directory.
    *   For local paths, it should behave like `Path.glob()`, yielding matches without recursing into subdirectories unless the pattern includes a directory separator.
    *   For remote filesystem paths, query the remote filesystem using the glob pattern.

*   Add the `xpathrglob` function to `src/datasets/utils/streaming_download_manager.py`:
    *   Ensure it is importable from `datasets.utils.streaming_download_manager`.
    *   Accepts a `path` (Path object) and a `pattern` (string).
    *   Recursively yields `Path` objects matching the pattern in the directory and all of its subdirectories.
    *   For local paths, it should behave like `Path.rglob()`, recursing into all subdirectories.
    *   For remote filesystem paths, search all subdirectories recursively, equivalent to calling `xpathglob` with "**/" prepended to the pattern.
    *   Ensure it finds files in nested subdirectories even when the parent directory does not directly contain matching files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.