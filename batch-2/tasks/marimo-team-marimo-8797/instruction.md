I'm working in an environment where all Python-generated cache files are redirected to a separate directory (using a system-level Python setting for this purpose).

*   The notebook_output_dir function must accept None, a string path, or a Path object. When None is passed, it must return Path('__marimo__') regardless of any system cache prefix setting.

*   When notebook_output_dir receives a file path, it must return the sibling __marimo__ directory (i.e., file.parent / '__marimo__'). When it receives a directory path, it must return that directory's __marimo__ subdirectory.

*   When sys.pycache_prefix is set to a non-None value and the resolved notebook path is absolute, notebook_output_dir must mirror the notebook's parent directory tree under the prefix by stripping the root component from the parent path and appending __marimo__. The resulting path must be creatable with mkdir(parents=True, exist_ok=True).

*   When sys.pycache_prefix is None, or when the notebook path is None, or when the notebook path resolves to a relative path, notebook_output_dir must ignore the prefix and return the normal __marimo__ sibling/subdirectory path.

*   The default_opengraph_image_abs function must return an absolute Path object. When sys.pycache_prefix is set, the returned path must be located under the prefix (not adjacent to the notebook). The returned path must have name equal to 'opengraph.png', and the notebook's stem (filename without extension) must appear as a component in the path.

*   The _default_image_exists function must accept a notebook path string and return True if and only if the file at the path returned by default_opengraph_image_abs exists. It must work correctly when sys.pycache_prefix is set, finding the image at its relocated path.

*   The resolve_opengraph_metadata function must use default_opengraph_image_abs to locate the default opengraph image file on disk, while the resolved.image value must still equal the result of default_opengraph_image for the same notebook path.

*   The FileStore class must initialize a _resolved_save_path attribute to None at construction time. The save_path property must lazily resolve the path on first access, setting _resolved_save_path to a non-None value. Before save_path is accessed, _resolved_save_path must remain None.

*   The get_session_cache_file function must, when sys.pycache_prefix is set, return a path structured as: prefix / relative_parent / '__marimo__' / 'session' / '<notebook_filename>.json', where relative_parent is the notebook's parent directory with its root component stripped (Path(*path.parent.parts[1:])).


*   Interface details: Type: Function
Name: notebook_output_dir
Location: marimo/_utils/paths.py
Signature: notebook_output_dir(path: str | Path | None) -> Path
Description: Returns the __marimo__ output directory for the given notebook path. When path is None, returns Path("__marimo__") (CWD-relative). When path is a file, returns the sibling __marimo__ directory. When path is a directory, returns the __marimo__ subdirectory inside it. When sys.pycache_prefix is set and the resolved path is absolute, mirrors the notebook's parent directory tree under the prefix (strips the root component) and appends __marimo__. Ignores sys.pycache_prefix when path is None or resolves to a relative path.

Type: Function
Name: default_opengraph_image_abs
Location: marimo/_metadata/opengraph.py
Signature: default_opengraph_image_abs(notebook_path: str) -> Path
Description: Returns the absolute Path where the default opengraph image should be stored for a given notebook. When sys.pycache_prefix is set, the path is located under the prefix (not next to the notebook). The returned path always has name == "opengraph.png" and the notebook stem (filename without extension) appears as a component in the path (used as a subdirectory).

Type: Function
Name: _default_image_exists
Location: marimo/_metadata/opengraph.py
Signature: _default_image_exists(notebook_path: str) -> bool
Description: Returns True if the default opengraph image file exists for the given notebook path. Uses default_opengraph_image_abs to locate the image so that it works correctly when sys.pycache_prefix is set.

Type: Class
Name: FileStore
Location: marimo/_save/stores/file.py
Description: File-based key-value store. Must expose a _resolved_save_path attribute initialized to None at construction time. The save_path property must lazily resolve and set _resolved_save_path on first access. After save_path is accessed at least once, _resolved_save_path must be non-None.
Signature: save_path (property) -> Path-like

Type: Function
Name: get_session_cache_file
Location: marimo/_session/state/serialize_session.py
Description: Returns the Path to the session cache file for a given notebook path. When sys.pycache_prefix is set, the returned path must be: prefix / relative_parent / "__marimo__" / "session" / "notebook.py.json" where relative_parent is the notebook's parent directory path with the root component stripped (Path(*path.parent.parts[1:])).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.