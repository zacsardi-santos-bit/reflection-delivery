Implement a robust restart function for the Salt minion that intelligently handles service-managed systems like systemd on Linux and Windows services. Enhance the function to optionally schedule a retry attempt if the initial restart fails, providing resilience against transient failures.

*   Implement the `_is_systemd_system` function:
    *   Return `True` if the system is Linux and systemd is booted.
    *   Return `False` otherwise.

*   Implement the `_is_windows_system` function:
    *   Return `True` if the system is Windows.
    *   Return `False` for other systems.

*   Implement the `_schedule_retry_systemd` function:
    *   Return `False` if not on a systemd system.
    *   On systemd, execute `cmd.run_all` with `systemd-run` and appropriate arguments to schedule a retry.
    *   Return a truthy value on success.

*   Implement the `_schedule_retry_windows` function:
    *   Return `False` if not on a Windows system.
    *   On Windows, use `task.create_task` with specified parameters to schedule a retry.

*   Implement the `_schedule_retry` function:
    *   Raise `CommandExecutionError` for non-numeric delay values.
    *   Return `False` if the system is neither systemd nor Windows.
    *   Dispatch to `_schedule_retry_systemd` or `_schedule_retry_windows` based on the system.

*   Implement the `restart` function with parameters: `systemd=True`, `win_service=True`, `schedule_retry=False`, `retry_delay=180`:
    *   On systemd systems, call `service.restart('salt-minion', no_block=True)` if `systemd` is not `False`.
    *   On Windows systems, call `service.restart('salt-minion')` if `win_service` is not `False`.
    *   Use `kill()` on non-service systems or if service management is disabled.
    *   If `-d` flag is present in the command-line, execute `cmd.run_all` with the original arguments.

*   Handle `schedule_retry`:
    *   Call `_schedule_retry(retry_delay)` before `service.restart` if `schedule_retry=True`.
    *   Abort restart and return error details if `_schedule_retry` fails.
    *   Include retry details in the return value if `_schedule_retry` succeeds.

*   Return a structured result dictionary with:
    *   `retcode` indicating success or failure.
    *   `comment` providing human-readable status.
    *   `service_restart` details, including `schedule_retry` info if applicable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.