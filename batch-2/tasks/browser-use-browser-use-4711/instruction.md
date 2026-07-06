Implement timeout protection for browser automation agents to prevent indefinite hangs when remote browsers become unresponsive. Introduce configurable timeouts for both individual browser protocol requests and tool action executions, ensuring these timeouts handle invalid configurations gracefully.

*   Create a new module at `browser_use/browser/_cdp_timeout.py`:
    *   Export `TimeoutWrappedCDPClient`, `DEFAULT_CDP_REQUEST_TIMEOUT_S`, `_coerce_valid_timeout`, and `_parse_env_cdp_timeout`.
    *   Implement `TimeoutWrappedCDPClient` as a subclass of `CDPClient`:
        *   Wrap the `send_raw` method with an asyncio timeout.
        *   Store timeout duration in `_cdp_request_timeout_s`.
        *   Raise `TimeoutError` with the CDP method name and "within" if the timeout is exceeded.
        *   Return the parent's result unchanged if it responds quickly.
    *   Define `DEFAULT_CDP_REQUEST_TIMEOUT_S` as a float constant between 10.0 and 120.0.
    *   Implement `_coerce_valid_timeout(value)`:
        *   Return `DEFAULT_CDP_REQUEST_TIMEOUT_S` for invalid values (None, NaN, inf, -inf, 0.0, or non-positive).
        *   Return the value unchanged for valid finite positive floats.
    *   Implement `_parse_env_cdp_timeout(s)`:
        *   Return 60.0 for invalid strings (None, empty, non-numeric, 'nan', 'NaN', 'inf', '-inf', '0', '-5', or non-positive).
        *   Return `float(s)` for valid finite positive numeric strings.

*   Update `browser_use/browser/session.py`:
    *   Import `TimeoutWrappedCDPClient` from `browser_use.browser._cdp_timeout`.
    *   Use `TimeoutWrappedCDPClient` for CDP connections to ensure timeout protection.

*   Modify `Tools` class in `browser_use/tools/service.py`:
    *   Update `act()` method signature to accept `action_timeout` (float).
    *   Return `ActionResult` with an error message if the action handler exceeds `action_timeout`.
    *   Return the handler's `ActionResult` unchanged if completed within `action_timeout`.
    *   Fall back to `_DEFAULT_ACTION_TIMEOUT_S` for invalid `action_timeout` values (nan, inf, -inf, 0, or negative).

*   Define `_DEFAULT_ACTION_TIMEOUT_S` in `browser_use/tools/service.py`:
    *   Set to >= 150.0 to avoid premature termination of slow operations.
    *   Fall back to 180.0 for invalid `BROWSER_USE_ACTION_TIMEOUT_S` values.
    *   Set to the float value of valid `BROWSER_USE_ACTION_TIMEOUT_S` strings.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.