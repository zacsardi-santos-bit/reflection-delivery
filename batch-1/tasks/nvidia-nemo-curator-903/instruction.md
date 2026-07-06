Implement support for reading video files from remote storage in the video data curation pipeline. Create a utility for handling filesystem paths and a new file partitioning stage that works with both local and remote storage backends.

*   Implement the `FSPath` class in `ray-curator/ray_curator/utils/client_utils.py`:
    *   Initialize with a filesystem instance and a path string, storing them as `_fs` and `_path`.
    *   Implement `__str__()` to return the path string and `__repr__()` to return `'FSPath({path})'`.
    *   Implement `open(mode, **kwargs)` to delegate to `self._fs.open(self._path, mode, **kwargs)`.
    *   Implement `as_posix()` to return a protocol-prefixed string for non-local filesystems, or the raw path for local filesystems.
    *   Implement `get_bytes_cat_ranges(*, part_size=10*1024**2)` to read the file in parallel byte-range chunks.

*   Create the `is_remote_url` function in `ray-curator/ray_curator/utils/client_utils.py`:
    *   Return `True` for remote storage URLs and `False` for local paths.

*   Develop the `ClientPartitioningStage` dataclass in `ray-curator/ray_curator/stages/client_partitioning.py`:
    *   Inherit from `FilePartitioningStage`.
    *   Use default values for attributes like `input_list_json_path=None`, `_name='client_partitioning'`, and others.
    *   Implement `setup()` to initialize `_fs` and `_root` using `url_to_fs`.
    *   Implement `process(_EmptyTask)` to raise `RuntimeError` if `setup()` has not been called.
    *   Implement `process()` to filter files by extension, apply limits, group files, and return `FileGroupTask` instances.

*   Implement the `_read_list_json_rel` function in `ray-curator/ray_curator/stages/client_partitioning.py`:
    *   Open a JSON file via `fsspec.open`, read paths, and return them relative to the root.
    *   Raise `ValueError` if any path is not under the root.

*   Update `VideoReaderStage.process()` to store the raw input value from `task.data[0]` in `video.input_video` without converting it to a `pathlib.Path`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.