Update the project initialization process to correctly reference the dedicated build backend package in the generated configuration file. Ensure that the `pyproject.toml` file reflects the appropriate build backend and dependencies.

*   Modify the project initialization command to update the `[build-system]` section of `pyproject.toml`:
    *   Set `build-backend = "uv_build"` instead of `"uv"`.
    *   Set the `requires` field to include `uv_build` with version specifiers `>=<current_version>,<next_minor_version>`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.