Implement WebSocket API commands to manage the Z-Wave JS driver's logging configuration. Create commands to retrieve and update log settings, ensuring robust input validation and appropriate error handling.

*   Export the following string constants in `homeassistant/components/zwave_js/api.py`:
    *   `CONFIG` with value "config"
    *   `LEVEL` with value "level"
    *   `LOG_TO_FILE` with value "log_to_file"
    *   `FILENAME` with value "filename"
    *   `ENABLED` with value "enabled"
    *   `FORCE_CONSOLE` with value "force_console"

*   Implement the WebSocket command `zwave_js/update_log_config`:
    *   Register the command with the WebSocket API.
    *   Accept parameters: `entry_id` (string) and `config` (object).
    *   Ensure `config` contains at least one of the following fields:
        *   `level` (string)
        *   `log_to_file` (boolean)
        *   `filename` (string)
        *   `force_console` (boolean)
        *   `enabled` (boolean)
    *   Validate `config`:
        *   If empty, return an error message containing "must contain at least one of".
        *   Validate `level` as a case-insensitive string matching known log levels. Return an error message containing "value must be one of" for unrecognized values.
        *   If `log_to_file` is True, ensure `filename` is provided. Return an error message containing "must be provided if logging to file" if absent.
    *   On success, call the zwave-js driver with `update_log_config` using camelCase keys and integer `level`.

*   Implement the WebSocket command `zwave_js/get_log_config`:
    *   Register the command with the WebSocket API.
    *   Accept parameter: `entry_id` (string).
    *   Return the current log configuration as a dictionary with snake_case keys:
        *   `enabled` (bool)
        *   `level` (integer, LogLevel enum value)
        *   `log_to_file` (bool)
        *   `filename` (str)
        *   `force_console` (bool)

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.