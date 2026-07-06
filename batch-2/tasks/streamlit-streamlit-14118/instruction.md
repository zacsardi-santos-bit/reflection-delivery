I'm looking to add a way to suppress Streamlit's startup welcome message through a configuration setting.

*   A new boolean configuration option `logger.hideWelcomeMessage` must be added to Streamlit's config system under the `logger` section, placed alphabetically between `logger.enableRich` and `logger.level` in the list of all config option keys.

*   When `logger.hideWelcomeMessage` is set to `True`, the `_print_url` function in `lib/streamlit/web/bootstrap.py` must produce no output to stdout — the full welcome message and URL output must be suppressed entirely.

*   When `logger.hideWelcomeMessage` is not set or is `False`, the existing `_print_url` behavior (printing the welcome message and URL) must remain unchanged.


*   Interface details: Type: ConfigOption
Name: logger.hideWelcomeMessage
Location: lib/streamlit/config.py
Signature: Boolean config option under the "logger" section, defaulting to False
Description: A new configuration key that, when set to True, suppresses all startup welcome/URL output. Must be registered in the config system so it appears alphabetically between "logger.enableRich" and "logger.level" in the full list of config option keys.

Type: Function
Name: _print_url
Location: lib/streamlit/web/bootstrap.py
Signature: _print_url(is_running_hello: bool) -> None
Description: Existing function that prints the welcome message and URL on startup. Must be modified to check the logger.hideWelcomeMessage config option and return without producing any output when that option is True.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.