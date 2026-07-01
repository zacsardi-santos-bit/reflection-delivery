Implement a system to declare Python package dependencies for local sandbox configurations, allowing automatic setup of the virtual environment. Ensure the configuration can be created without specifying a directory, defaulting to a sensible location, and provide a REST API endpoint for managing these configurations.

*   Add a `PipRequirement` model in `letta/schemas/sandbox_config.py`:
    *   Include a required non-empty string field `name`.
    *   Include an optional string field `version`, defaulting to `None`.
    *   Ensure `version` is serialized as `{"name": "...", "version": null}` when `None`.

*   Update `LocalSandboxConfig` in `letta/schemas/sandbox_config.py`:
    *   Add an optional `pip_requirements` field, a list of `PipRequirement`, defaulting to an empty list.
    *   Make `sandbox_dir` optional, defaulting to `tool_settings.local_sandbox_dir` or `~/.letta` if not provided.

*   Implement a POST endpoint at `/v1/sandbox-config/local`:
    *   Accept a `LocalSandboxConfig` JSON body.
    *   Return HTTP 200 with a `SandboxConfig` response object.
    *   Ensure the response includes the serialized `LocalSandboxConfig` with `pip_requirements`.

*   Ensure `PipRequirement`, `LocalSandboxConfig`, and `SandboxConfig` are importable from `letta.schemas.sandbox_config`.

*   Verify that creating a `SandboxConfigCreate` with `LocalSandboxConfig()` without arguments succeeds:
    *   The stored config must have `type` as `SandboxType.LOCAL`.
    *   The `sandbox_dir` should be `~/.letta` or `tool_settings.local_sandbox_dir`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.