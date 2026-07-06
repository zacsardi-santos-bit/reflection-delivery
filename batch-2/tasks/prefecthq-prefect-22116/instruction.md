I'm working with Prefect runners and I've noticed that when a runner pulls a deployment whose code includes a Python project definition, it automatically tries to find and use a package manager to install dependencies before starting the flow run.

*   A new boolean setting `PREFECT_RUNNER_AUTO_INSTALL_DEPENDENCIES` must be added to the Prefect settings system and must be importable from `prefect.settings`. Its default value must be `False`.

*   When `PREFECT_RUNNER_AUTO_INSTALL_DEPENDENCIES` is `False` (the default), `_workspace_command` in `prefect.runner._workspace_starter` must return `None` immediately without inspecting the workspace for a pyproject.toml, checking dependencies, or calling `shutil.which` to locate uv.

*   When `PREFECT_RUNNER_AUTO_INSTALL_DEPENDENCIES` is `True` and all conditions are met (pyproject.toml exists at the workspace project root, the project declares `prefect` as a dependency, and `uv` is found via the workspace PATH), `_workspace_command` must return a command string whose parsed form is `[uv_executable, 'run', '--no-default-groups', '--project', str(project_root), '-m', 'prefect.flow_engine']`.

*   When `PREFECT_RUNNER_AUTO_INSTALL_DEPENDENCIES` is `True` but any of the required conditions fail (missing pyproject.toml, missing prefect dependency, or uv not found via workspace PATH), `_workspace_command` must return `None`.

*   The `PREFECT_RUNNER_AUTO_INSTALL_DEPENDENCIES` setting must support runtime overrides via the `temporary_settings` context manager.

*   The `PREFECT_RUNNER_AUTO_INSTALL_DEPENDENCIES` setting must be registered in the supported settings dictionary, with a boolean test value of `True`.


*   Interface details: Type: Setting
Name: PREFECT_RUNNER_AUTO_INSTALL_DEPENDENCIES
Location: src/prefect/settings/models/runner.py (defined as `auto_install_dependencies` field on the runner settings model)
Signature: bool, default=False
Description: Controls whether the runner may install project dependencies before executing flow runs. When False (the default), the workspace command builder returns None immediately without checking for pyproject.toml, dependency declarations, or uv availability. When True, the runner checks for a pyproject.toml declaring a prefect dependency and a uv executable on the workspace PATH, and if all conditions are met, builds a `uv run --no-default-groups --project <project_root> -m prefect.flow_engine` command.

Note: This setting must be exported from `prefect.settings` as `PREFECT_RUNNER_AUTO_INSTALL_DEPENDENCIES` so that it can be imported and used with `temporary_settings`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.