Implement support for fetching customer service case events in the ServiceNow event collector integration. Refactor the handling of log types to use a structured representation, enabling easier maintenance and expansion. Ensure all related functions and methods work with this new structure.

*   Introduce a `LogType` enum with members: `AUDIT`, `SYSLOG_TRANSACTIONS`, and `CASE`.
    *   Each member must have attributes: `type_string`, `last_fetch_time_key`, and `previous_ids_key`.
    *   Specific values:
        *   `AUDIT`: `type_string='audit'`, `last_fetch_time_key='last_fetch_time'`, `previous_ids_key='previous_run_ids'`
        *   `SYSLOG_TRANSACTIONS`: `last_fetch_time_key='last_fetch_time_syslog'`, `previous_ids_key='previous_run_ids_syslog'`
        *   `CASE`: `type_string='case'`, `last_fetch_time_key='last_fetch_time_case'`, `previous_ids_key='previous_run_ids_case'`

*   Update functions to use the `LogType` enum:
    *   `enrich_events(events: list[dict], log_type: LogType) -> list[dict]`
        *   Add `_time` and `source_log_type` fields to each event.
        *   Raise `ValueError` for invalid `sys_created_on` format and `KeyError` if missing.
    *   `deduplicate_events(events: list[dict], previous_run_ids: set, from_date: str) -> tuple[list[dict], set]`
        *   Filter duplicates and manage ID sets based on `sys_created_on`.
    *   `get_from_date(last_run: dict, log_type: LogType) -> str`
        *   Return the last fetch time or a default timestamp.
    *   `get_log_types_from_titles(event_types_to_fetch: list[str]) -> list[LogType]`
        *   Convert title strings to `LogType` members, raising `DemistoException` for invalid titles.
    *   `get_limit(args: dict, client: Client, log_type: LogType) -> int`
        *   Determine fetch limit based on `args`, `client.fetch_limits`, or default to 1000.
    *   `update_last_run(last_run: dict, log_type: LogType, last_event_time: str, previous_run_ids: list) -> dict`
        *   Update `last_run` with new fetch time and IDs.

*   Modify the `Client` class:
    *   Update constructor parameters and introduce `fetch_limits`.
    *   Implement `_get_api_url(log_type: LogType) -> str` to construct API URLs based on `log_type`.
    *   Ensure `search_events` uses `_get_api_url` and appropriate parameters.

*   Implement command functions:
    *   `get_events_command(client: Client, args: dict, log_type: LogType, last_run: dict) -> tuple[list, CommandResults]`
        *   Fetch and process events for a single log type.
    *   `fetch_events_command(client: Client, last_run: dict, log_types: list[LogType]) -> tuple[list, dict]`
        *   Fetch and process events for multiple log types.
    *   `main` function to route commands to appropriate handlers based on `LogType`.

*   Ensure backward compatibility by replacing old string-based functions with `LogType`-aware versions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.