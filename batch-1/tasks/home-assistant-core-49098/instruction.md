Implement a system to safely handle stop and restart operations in Home Assistant, ensuring they do not occur during a database migration. Create a helper to check migration status and modify services to prevent unsafe operations.

*   Implement the helper function `async_migration_in_progress` in `homeassistant/helpers/recorder.py`:
    *   Return `False` if the recorder component is not loaded.
    *   Delegate to `homeassistant.components.recorder.async_migration_in_progress(hass)` and return its result if the recorder is loaded.

*   Implement `async_migration_in_progress` in `homeassistant/components/recorder/__init__.py`:
    *   Return `False` if no recorder instance exists in `hass.data`, keyed by `DATA_INSTANCE`.
    *   Return the value of the `migration_in_progress` attribute if the instance exists.

*   On the Recorder instance class in `homeassistant/components/recorder/__init__.py`:
    *   Initialize an `async_migration_event` attribute as an `asyncio.Event`.
    *   Set `async_migration_event` when a database migration starts.
    *   Initialize a `migration_in_progress` boolean attribute to `False`.
    *   Set `migration_in_progress` to `True` at the start of a migration and reset it to `False` when the migration ends.

*   Modify the stop and restart services in the Home Assistant component:
    *   Check if a database migration is in progress before executing.
    *   Raise `HomeAssistantError` if a migration is in progress.
    *   For the restart service, validate the configuration before restarting and raise `HomeAssistantError` if invalid.
    *   The stop service should not validate configuration.

*   Execute stop and restart services asynchronously with a short delay:
    *   Schedule the actual stop/restart to occur 2 seconds after the service call.

*   Ensure the restart service, when invoked via the WebSocket API, is called with `blocking=True`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.