I'm working on improving how Airflow handles remote task logging configuration.

*   A RemoteLoggingInfo data class must be defined with fields classpath (str), scheme (str), and package_name (str), support equality comparison, and be importable from both airflow.providers_manager (core) and airflow.sdk.providers_manager_runtime (task-sdk).

*   Both ProvidersManager (core) and ProvidersManagerTaskRuntime (task-sdk) must implement a _discover_remote_logging() method that reads provider entries under the 'remote-logging' key. Each entry must have 'classpath' and 'scheme' string keys. The method must try to import each classpath and silently skip any entry whose classpath cannot be imported.

*   _discover_remote_logging() must populate two attributes on the manager: _remote_logging_info_list (a list of RemoteLoggingInfo instances) and _remote_logging_by_scheme (a dict mapping scheme string to RemoteLoggingInfo). When multiple providers register the same scheme, the first registration wins and the duplicate is ignored — it must not appear in either _remote_logging_info_list or _remote_logging_by_scheme.

*   A new shared logging factory module must exist and be importable as both airflow_shared.logging.factory and airflow._shared.logging.factory. It must export: DEFAULT_LOGGING_CONFIG_PATH (a string constant), _build_remote_task_log_from_provider (function), and resolve_remote_task_log (function).

*   _build_remote_task_log_from_provider(remote_base_log_folder, providers_manager, import_string) must return None when remote_base_log_folder is None, when the URL has no scheme, or when the scheme is not found via providers_manager.remote_logging_handler_by_scheme(scheme). It must also return None when the resolved handler class lacks a from_config classmethod. When all conditions are met, it must call the handler class's from_config() classmethod and return the result.

*   resolve_remote_task_log(conf, providers_manager, import_string) must return a (handler, conn_id) tuple using three-tier precedence: (1) if the user has configured a non-default logging_config_class and that module exposes REMOTE_TASK_LOG, return (REMOTE_TASK_LOG, DEFAULT_REMOTE_CONN_ID) and skip provider dispatch entirely; (2) if conf.getboolean('logging', 'remote_logging') is True, attempt provider dispatch and return (handler, None) on success; (3) fall back to reading REMOTE_TASK_LOG and DEFAULT_REMOTE_CONN_ID from the module referenced by DEFAULT_LOGGING_CONFIG_PATH.

*   When conf.getboolean('logging', 'remote_logging') is False, resolve_remote_task_log must not call providers_manager.remote_logging_handler_by_scheme at all — it must skip provider dispatch entirely.

*   _ActiveLoggingConfig must be a class in airflow.logging_config with class-level attributes logging_config_loaded (bool, default False), remote_task_log (default None), and default_remote_conn_id (default None). It must have a classmethod set(remote_task_log, default_remote_conn_id) that assigns both values and sets logging_config_loaded to True.

*   _get_logging_config() must return the default logging config dict (DEFAULT_LOGGING_CONFIG from airflow.config_templates.airflow_local_settings) when the configured logging_config_class path is falsy (empty string, None, or not set). When a module.ATTR path is configured, it must import and return the attribute value if it is a dict. It must raise ImportError with a message matching 'Unable to load custom logging config' for invalid or unimportable paths, and raise ImportError with a message matching 'Logging Config should be of dict type' when the imported value is not a dict.

*   _load_logging_config() must call resolve_remote_task_log with providers_manager=ProvidersManager() passed as a keyword argument, then store the returned (handler, conn_id) into _ActiveLoggingConfig (setting remote_task_log, default_remote_conn_id, and logging_config_loaded=True).

*   load_logging_config() must emit a DeprecationWarning with a message matching 'load_logging_config is deprecated', still call _load_logging_config() to prime the remote handler cache, and return a (logging_config_dict, logging_class_path) tuple where logging_class_path is the user-configured path if set, or DEFAULT_LOGGING_CONFIG_PATH otherwise.

*   get_remote_task_log() must return _ActiveLoggingConfig.remote_task_log directly without calling _load_logging_config() when _ActiveLoggingConfig.logging_config_loaded is True. When not loaded, it must call _load_logging_config() first and then return the cached value.

*   get_default_remote_conn_id() must return the value from conf immediately (without calling _load_logging_config()) when conf provides a truthy value. When conf returns None/falsy and loading has not occurred, it must call _load_logging_config() and return _ActiveLoggingConfig.default_remote_conn_id. When _ActiveLoggingConfig.logging_config_loaded is already True, it must skip the reload and return from cache.


*   Interface details: Type: Class
Name: RemoteLoggingInfo
Location: airflow-core/src/airflow/providers_manager.py
Description: A data class (or named tuple) representing a registered remote logging handler. Must support equality comparison. Fields: classpath (str), scheme (str), package_name (str).
Signature: RemoteLoggingInfo(classpath: str, scheme: str, package_name: str)

Type: Class
Name: RemoteLoggingInfo
Location: task-sdk/src/airflow/sdk/providers_manager_runtime.py
Description: Same RemoteLoggingInfo data class as in the core providers manager, but importable from the task-SDK runtime providers manager. Fields: classpath (str), scheme (str), package_name (str). Must support equality comparison.
Signature: RemoteLoggingInfo(classpath: str, scheme: str, package_name: str)

Type: Method
Name: _discover_remote_logging
Location: airflow-core/src/airflow/providers_manager.py (on ProvidersManager class)
Description: Discovers remote logging handlers from registered provider data. Reads entries under the "remote-logging" key from each provider's data dict. Each entry has "classpath" and "scheme" keys. Tries to import each classpath; silently skips entries whose classpath cannot be imported. First-wins for duplicate schemes. Populates _remote_logging_info_list and _remote_logging_by_scheme.
Signature: _discover_remote_logging(self) -> None

Type: Attribute
Name: _remote_logging_info_list
Location: airflow-core/src/airflow/providers_manager.py (on ProvidersManager class)
Description: A list of RemoteLoggingInfo instances for all successfully discovered remote logging handlers. Empty if none were discovered or if classpaths were invalid.

Type: Attribute
Name: _remote_logging_by_scheme
Location: airflow-core/src/airflow/providers_manager.py (on ProvidersManager class)
Description: A dict mapping URL scheme strings (e.g. "s3", "gcs") to RemoteLoggingInfo instances for all successfully registered remote logging handlers.

Type: Method
Name: _discover_remote_logging
Location: task-sdk/src/airflow/sdk/providers_manager_runtime.py (on ProvidersManagerTaskRuntime class)
Description: Same behavior as ProvidersManager._discover_remote_logging: discovers remote logging from provider data, validates classpaths, first-wins for duplicates, populates _remote_logging_info_list and _remote_logging_by_scheme.
Signature: _discover_remote_logging(self) -> None

Type: Attribute
Name: _remote_logging_info_list
Location: task-sdk/src/airflow/sdk/providers_manager_runtime.py (on ProvidersManagerTaskRuntime class)
Description: List of RemoteLoggingInfo instances for successfully discovered remote logging handlers on the task-SDK runtime providers manager.

Type: Attribute
Name: _remote_logging_by_scheme
Location: task-sdk/src/airflow/sdk/providers_manager_runtime.py (on ProvidersManagerTaskRuntime class)
Description: Dict mapping URL scheme strings to RemoteLoggingInfo instances on the task-SDK runtime providers manager.

Type: Module
Name: airflow_shared.logging.factory
Location: shared/logging/src/airflow_shared/logging/factory.py
Description: New module providing shared logging factory utilities. Must also be importable as airflow._shared.logging.factory (via a bridge in airflow-core or namespace package). Contains DEFAULT_LOGGING_CONFIG_PATH, _build_remote_task_log_from_provider, and resolve_remote_task_log.

Type: Constant
Name: DEFAULT_LOGGING_CONFIG_PATH
Location: shared/logging/src/airflow_shared/logging/factory.py
Description: A string constant holding the dotted module.ATTR path to the default logging configuration object (e.g. "airflow.config_templates.airflow_local_settings.DEFAULT_LOGGING_CONFIG"). Must be importable from both airflow_shared.logging.factory and airflow._shared.logging.factory.

Type: Function
Name: _build_remote_task_log_from_provider
Location: shared/logging/src/airflow_shared/logging/factory.py
Description: Attempts to build a remote task log handler by looking up the URL scheme from the configured log folder in the providers manager. Returns the handler instance on success, or None on any failure (None URL, no scheme in URL, unknown scheme, missing from_config classmethod).
Signature: _build_remote_task_log_from_provider(remote_base_log_folder: str | None, providers_manager: Any, import_string: Callable) -> Any | None

Type: Function
Name: resolve_remote_task_log
Location: shared/logging/src/airflow_shared/logging/factory.py
Description: Determines the active remote task log handler and default connection ID using a three-tier precedence: (1) user-defined logging module exposing REMOTE_TASK_LOG wins; (2) provider dispatch via _build_remote_task_log_from_provider when remote_logging is enabled; (3) legacy fallback reading REMOTE_TASK_LOG/DEFAULT_REMOTE_CONN_ID from the default logging config module. Returns a (handler, conn_id) tuple — both may be None.
Signature: resolve_remote_task_log(conf: Any, providers_manager: Any, import_string: Callable) -> tuple[Any, str | None]

Type: Class
Name: _ActiveLoggingConfig
Location: airflow-core/src/airflow/logging_config.py
Description: A class used as a module-level singleton for caching the active logging configuration state. Holds class-level attributes: logging_config_loaded (bool, default False), remote_task_log (default None), default_remote_conn_id (default None). The classmethod set() assigns both remote_task_log and default_remote_conn_id, and sets logging_config_loaded to True.
Signature: set(cls, remote_task_log: Any, default_remote_conn_id: str | None) -> None

Type: Function
Name: _get_logging_config
Location: airflow-core/src/airflow/logging_config.py
Description: Loads and returns the logging configuration dict. Returns the default logging config (DEFAULT_LOGGING_CONFIG) when no custom path is configured or when the path is falsy (empty string or None). When a custom module.ATTR path is configured via conf, imports and returns the attribute value if it is a dict. Raises ImportError with message matching "Unable to load custom logging config" for invalid/unimportable paths. Raises ImportError with message matching "Logging Config should be of dict type" when the imported value is not a dict.
Signature: _get_logging_config() -> dict

Type: Function
Name: _load_logging_config
Location: airflow-core/src/airflow/logging_config.py
Description: Internal function that calls resolve_remote_task_log with a ProvidersManager() instance as the providers_manager keyword argument, then stores the returned (handler, conn_id) in _ActiveLoggingConfig by setting remote_task_log, default_remote_conn_id, and logging_config_loaded=True.
Signature: _load_logging_config() -> None

Type: Function
Name: load_logging_config
Location: airflow-core/src/airflow/logging_config.py
Description: Deprecated public wrapper for loading logging configuration. Emits a DeprecationWarning with a message matching "load_logging_config is deprecated". Calls _load_logging_config() to prime the remote handler cache. Returns a (logging_config_dict, logging_class_path) tuple. The logging_class_path is the user-configured path if set, otherwise DEFAULT_LOGGING_CONFIG_PATH.
Signature: load_logging_config() -> tuple[dict, str]

Type: Function
Name: get_remote_task_log
Location: airflow-core/src/airflow/logging_config.py
Description: Returns the active remote task log handler. If _ActiveLoggingConfig.logging_config_loaded is True, returns _ActiveLoggingConfig.remote_task_log directly without triggering a reload. Otherwise calls _load_logging_config() first and then returns the cached value.
Signature: get_remote_task_log() -> Any

Type: Function
Name: get_default_remote_conn_id
Location: airflow-core/src/airflow/logging_config.py
Description: Returns the default remote connection ID. First checks conf for an explicit value; if found and truthy, returns it immediately without calling _load_logging_config(). If conf returns None/falsy, and the config is not yet loaded, calls _load_logging_config() and returns _ActiveLoggingConfig.default_remote_conn_id. If already loaded (logging_config_loaded=True), skips reload and returns from cache.
Signature: get_default_remote_conn_id() -> str | None


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.