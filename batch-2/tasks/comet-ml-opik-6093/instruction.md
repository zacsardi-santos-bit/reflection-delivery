I'm working with the Opik SDK's configuration system and I've noticed that while the setup wizard handles API keys, workspaces, and server URLs, there's no way to set the default project name for trace logging as part of that same flow.

*   OPIK_PROJECT_DEFAULT_NAME must be defined as a string constant in opik/config.py and exported so it can be imported alongside OPIK_WORKSPACE_DEFAULT_NAME.

*   OpikConfig must accept a project_name keyword argument in its constructor. When OpikConfig is instantiated with project_name='<value>', that value must be accessible on the resulting instance.

*   OpikConfigurator must accept a project_name keyword argument in its constructor and expose it as self.project_name. When project_name is provided, it is stored on the instance for use during configuration.

*   _ask_for_project_name must prompt the user via input(), assign the result to self.project_name, and raise ConfigurationError with the message 'The project name cannot be empty' if the entered string is empty.

*   _set_project_name must implement a three-case resolution strategy and return a bool indicating whether the configuration needs to be updated. Case 1: if the user provided a project_name at construction, keep it and return False (or True when force=True). Case 2: if no user-provided name but current_config.project_name is a non-default, non-empty value and force=False, reuse that value and return False; if the config holds OPIK_PROJECT_DEFAULT_NAME or force=True, fall through to Case 3. Case 3: if automatic_approvals=True, silently use OPIK_PROJECT_DEFAULT_NAME and return True; otherwise prompt user via ask_user_for_approval — if approved use OPIK_PROJECT_DEFAULT_NAME and return True; if rejected call _ask_for_project_name() and return True.

*   _set_project_name must raise ConfigurationError with the message 'The project name cannot be empty' when the user rejects the default project name and then enters an empty string.

*   _update_config must pass project_name=self.project_name when constructing OpikConfig, and must call update_session_config('project_name', project_name) to persist the project name in the session.

*   _configure_cloud must call self._set_project_name() as part of its configuration flow. After _configure_cloud completes, self.project_name must reflect the chosen project name.

*   _configure_local must call self._set_project_name() as part of its configuration flow. When an explicit project_name is provided, it must be preserved on self.project_name after configuration. update_session_config('project_name', ...) must be called with the resolved project name.

*   The module-level configure() function must accept a project_name keyword argument. When project_name is provided along with api_key and workspace, the OPIK_PROJECT_NAME environment variable must be set to the provided project_name value.

*   When configure() or _update_config() runs with a project_name, update_session_config must be called with 'project_name' as the key and the resolved project name as the value.

*   The configuration success log message must read: "Configuration completed successfully. Traces will be logged to '{project_name}' project. To change the destination project, see: https://www.comet.com/docs/opik/tracing/log_traces#configuring-the-project-name", where {project_name} is the value of self.project_name (not self.current_config.project_name).

*   OpikConfig must accept project_name as a constructor argument when saving configuration. When all four parameters (api_key, url_override, workspace, project_name) are provided to configure(), OpikConfig must be instantiated with all four and save_to_file() must be called.

*   When configure() is called with force=True and automatic_approvals is not explicitly provided, the configuration must proceed without asking the user for approval — this means defaults for project name and workspace are auto-accepted. Specifically, configure(force=True) must result in OpikConfigurator being initialized with automatic_approvals=True (or equivalent behavior).

*   _set_workspace Case 3 (defaulting to the workspace default) must use automatic_approvals only to decide whether to prompt for approval — force alone must not bypass the approval prompt. The combined force+automatic_approvals=True behavior is the only way to skip workspace approval questions.


*   Interface details: Type: Constant
Name: OPIK_PROJECT_DEFAULT_NAME
Location: sdks/python/src/opik/config.py
Description: A string constant holding the default project name used when no project name is provided or when the config has no custom project name. Must be exported from opik.config alongside OPIK_WORKSPACE_DEFAULT_NAME.

Type: Class
Name: OpikConfig
Location: sdks/python/src/opik/config.py
Description: Configuration data class that must accept a project_name parameter.
Signature: OpikConfig(api_key=None, url_override=None, workspace=None, project_name=None, ...)

Type: Class
Name: OpikConfigurator
Location: sdks/python/src/opik/configurator/configure.py
Description: Configurator class that must accept a project_name keyword argument and expose a project_name instance attribute. Must call _set_project_name() during both _configure_cloud() and _configure_local() flows.
Signature: OpikConfigurator(api_key=None, workspace=None, url=None, project_name=None, force=False, automatic_approvals=False, ...)

Type: Method
Name: _ask_for_project_name
Location: sdks/python/src/opik/configurator/configure.py
Description: Instance method on OpikConfigurator. Prompts the user for a project name via input(), sets self.project_name to the entered value. Raises ConfigurationError with message "The project name cannot be empty" if the user enters an empty string.
Signature: _ask_for_project_name(self) -> None

Type: Method
Name: _set_project_name
Location: sdks/python/src/opik/configurator/configure.py
Description: Instance method on OpikConfigurator. Determines and sets the project name to use. Returns True if the configuration needs to be updated (i.e. something changed), False otherwise. Logic follows three cases:
  Case 1 – User explicitly provided project_name at construction time: keeps that name; returns False if force=False, True if force=True.
  Case 2 – No user-provided name, but current_config.project_name is a non-default, non-empty name AND force=False: reuses the config value, returns False. If config holds OPIK_PROJECT_DEFAULT_NAME or force=True, falls through to Case 3.
  Case 3 – No usable name from user or config: if automatic_approvals=True, silently uses OPIK_PROJECT_DEFAULT_NAME and returns True; otherwise prompts the user for approval of the default (via ask_user_for_approval) — if approved, uses OPIK_PROJECT_DEFAULT_NAME and returns True; if rejected, calls _ask_for_project_name() and returns True. Raises ConfigurationError("The project name cannot be empty") if the user enters an empty name.
Signature: _set_project_name(self) -> bool

Type: Function
Name: configure
Location: sdks/python/src/opik/configurator/configure.py
Description: Module-level configuration function. Must accept a project_name keyword argument. When project_name is supplied along with api_key and workspace, sets the OPIK_PROJECT_NAME environment variable. Calls update_session_config("project_name", ...) with the resolved project name.
Signature: configure(..., project_name=None, ...) -> None


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.