Implement utility functions in the `python/ray/workers/setup_runtime_env.py` module to support local development workflows with conda environments. Ensure these functions are available for import to prevent test failures.

*   Define the function `_current_py_version`:
    *   Location: `python/ray/workers/setup_runtime_env.py`
    *   Signature: `_current_py_version() -> str`
    *   Functionality: Return the currently-running Python version as a string in 'X.Y.Z' format (e.g., '3.8.10').

*   Define the function `_resolve_install_from_source_ray_dependencies`:
    *   Location: `python/ray/workers/setup_runtime_env.py`
    *   Signature: `_resolve_install_from_source_ray_dependencies() -> List[str]`
    *   Functionality: Return a list of Ray's pip install_requires dependencies as declared in the source tree's setup.py.

*   Define the function `_inject_ray_to_conda_site`:
    *   Location: `python/ray/workers/setup_runtime_env.py`
    *   Signature: `_inject_ray_to_conda_site(conda_path: str) -> None`
    *   Functionality: Accept a single string argument representing the path to a conda environment directory. Inject the currently-running Ray source path into that environment's site-packages by writing a .pth file.

*   Ensure all three functions are importable at module load time from `ray.workers.setup_runtime_env` without error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.