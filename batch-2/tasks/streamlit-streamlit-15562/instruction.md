I'm running Streamlit inside the Windows Subsystem for Linux and the automatic file-watching doesn't work reliably — changes to my files often don't trigger a reload.

*   The _is_wsl() function must return False when IS_LINUX_OR_BSD is False, even if WSL-related environment variables are present.

*   The _is_wsl() function must return True on Linux/BSD when the WSL_DISTRO_NAME environment variable is set.

*   The _is_wsl() function must return True on Linux/BSD when the WSL_INTEROP environment variable is set.

*   The _is_wsl() function must return True on Linux/BSD when /proc/version contains 'microsoft' (case-insensitive), covering standard WSL1 and WSL2 kernel strings.

*   The _is_wsl() function must return True on Linux/BSD when /proc/version contains 'wsl2' (case-insensitive), covering custom WSL2 kernels without 'microsoft' in the string.

*   The _is_wsl() function must return False on Linux/BSD when /proc/version contains only a bare 'wsl' substring (e.g. 'host-wsl-lab') that does not match 'microsoft' or 'wsl2'.

*   The _is_wsl() function must return False on Linux/BSD when /proc/version cannot be read due to an OSError.

*   IS_WSL must be a module-level boolean constant in streamlit.env_util, set to the result of _is_wsl() at module import time.

*   _WSL_POLLING_INFO must be a Final string constant exported from streamlit.watcher.path_watcher; it must be the exact string passed to click.secho with fg='blue' when the WSL polling message is shown.

*   _report_wsl_polling_once must be decorated with @functools.cache so it executes exactly once per process (subsequent calls are no-ops). It must display _WSL_POLLING_INFO via click.secho with fg='blue'.

*   When server.fileWatcherType is 'auto' and env_util.IS_WSL is True, report_watchdog_availability() must call _report_wsl_polling_once() and return early without calling _is_watchdog_available().

*   When server.fileWatcherType is 'auto' and env_util.IS_WSL is True, get_default_path_watcher_class() must return PollingPathWatcher without calling _is_watchdog_available().

*   When server.fileWatcherType is 'watchdog' and env_util.IS_WSL is True, get_default_path_watcher_class() must return EventBasedPathWatcher (explicit watcher config overrides the WSL polling default).


*   Interface details: Type: Function
Name: _is_wsl
Location: lib/streamlit/env_util.py
Signature: _is_wsl() -> bool
Description: Detects whether Streamlit is running inside the Windows Subsystem for Linux. Returns False immediately if IS_LINUX_OR_BSD is False. On Linux/BSD, returns True if the WSL_DISTRO_NAME or WSL_INTEROP environment variables are present. Otherwise reads /proc/version and returns True if it contains "microsoft" or "wsl2" (case-insensitive). Returns False if /proc/version is unreadable (OSError) or if the file only contains a bare "wsl" substring that doesn't match "microsoft" or "wsl2".

Type: Constant
Name: IS_WSL
Location: lib/streamlit/env_util.py
Signature: IS_WSL: bool
Description: Module-level boolean constant set to the result of _is_wsl() at import time. Referenced as streamlit.env_util.IS_WSL.

Type: Constant
Name: _WSL_POLLING_INFO
Location: lib/streamlit/watcher/path_watcher.py
Signature: _WSL_POLLING_INFO: Final[str]
Description: String constant with the informational message displayed to the user when WSL polling mode is activated under "auto" configuration. Imported directly by tests and used as the expected argument to click.secho with fg="blue".

Type: Function
Name: _report_wsl_polling_once
Location: lib/streamlit/watcher/path_watcher.py
Signature: _report_wsl_polling_once() -> None
Description: Displays _WSL_POLLING_INFO via click.secho with fg="blue". Must be decorated with @functools.cache so it is idempotent — it only shows the message once per process. Tests call _report_wsl_polling_once.cache_clear() in setUp to reset this state between test runs.

Type: Function
Name: report_watchdog_availability
Location: lib/streamlit/watcher/path_watcher.py
Signature: report_watchdog_availability() -> None
Description: Existing function with modified behavior: when server.fileWatcherType is "auto" and env_util.IS_WSL is True, must call _report_wsl_polling_once() and return early without invoking _is_watchdog_available(). When IS_WSL is False, existing behavior is unchanged.

Type: Function
Name: get_default_path_watcher_class
Location: lib/streamlit/watcher/path_watcher.py
Signature: get_default_path_watcher_class() -> PathWatcherType
Description: Existing function with modified behavior: when server.fileWatcherType is "auto" and env_util.IS_WSL is True, must return PollingPathWatcher without calling _is_watchdog_available(). When server.fileWatcherType is "watchdog" and IS_WSL is True, must still return EventBasedPathWatcher (explicit config overrides WSL default).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.