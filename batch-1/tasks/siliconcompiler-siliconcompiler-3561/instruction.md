Implement a unified class-based interface for tool management in SiliconCompiler. This class should consolidate operations such as finding executables, checking versions, building environment variables, and constructing command lines. Ensure the class is part of the public API and can bind to a chip's runtime context, raising errors if necessary configurations are missing.

*   Create a `ToolSchema` class in `siliconcompiler/tool.py` and ensure it is importable from `siliconcompiler`.
    *   Define `__init__(self, name=None)`.
    *   Implement `set_runtime(self, chip)` to bind to a chip context, raising `RuntimeError` for missing configurations.
    *   Implement `node(self)`, `task(self)`, `logger(self)`, and `schema(self, type=None)` methods for accessing runtime state and schema.
    *   Implement `get_exe(self)` to locate executables, raising `TaskExecutableNotFound` if not found.
    *   Implement `get_exe_version(self)` to check executable versions, handling subprocess execution and logging.
    *   Implement `check_exe_version(self, reported_version)` to validate versions against PEP-440 specifiers.
    *   Implement `get_runtime_environmental_variables(self, include_path=True)` to assemble environment variables.
    *   Implement `get_runtime_command(self)` to construct command lists for execution.
    *   Implement `parse_version(self, stdout)`, `normalize_version(self, version)`, and `runtime_options(self)` with default behaviors and allow subclass overrides.
    *   Ensure deep copying resets runtime state.

*   Create a `TaskSchema` class in `siliconcompiler/tool.py` for task representation.
    *   Define `__init__(self, name=None)`.

*   Create a `TaskExecutableNotFound` exception in `siliconcompiler/tool.py` for missing executables.

*   Import `shutil` and `subprocess` at the module level in `siliconcompiler/tool.py`.

*   Update tool-level schema parameters (`exe`, `format`, `licenseserver`, `path`, `sbom`, `vendor`, `version`, `vswitch`) to have global scope and update the schema version to "0.51.2".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.