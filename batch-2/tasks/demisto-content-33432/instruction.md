Implement an event collector integration for the Code42 insider risk platform to automatically pull security events into Cortex XSIAM. Authenticate using OAuth credentials and periodically fetch file activity events and administrator audit logs from the Code42 API. Ensure events are tagged with their type and sent in separate batches to the XSIAM ingestion pipeline, while tracking state to avoid re-sending events.

*   Export a `DATE_FORMAT` constant for datetime formatting in `Packs/Code42/Integrations/Code42EventCollector/Code42EventCollector.py`.
*   Define an `EventType` enum with values `FILE` and `AUDIT` in `Packs/Code42/Integrations/Code42EventCollector/Code42EventCollector.py`.
    *   Ensure each event dictionary includes an 'eventType' field set to the appropriate `EventType` value.
*   Create a `FileEventLastRun` class with `FETCHED_IDS` and `TIME` attributes for the last-run state dictionary keys for file events.
*   Create an `AuditLogLastRun` class with `FETCHED_IDS` and `TIME` attributes for the last-run state dictionary keys for audit logs.
*   Implement the `main()` function in `Packs/Code42/Integrations/Code42EventCollector/Code42EventCollector.py` to handle commands:
    *   "test-module": Call `return_results` with "ok".
    *   "fetch-events": Call `send_events_to_xsiam` twice, first with file events, then audit logs.
    *   "code42-get-events": Return a `CommandResult` with events of the specified type.
*   Ensure `fetch-events` respects `max_file_events_per_fetch` and `max_audit_events_per_fetch` parameters.
*   Implement a connectivity test to verify API reachability and credential validity.
*   Implement `code42-get-events` command to fetch events by type and start date, returning structured and readable outputs.
*   Use POST to `/v1/oauth` for authentication and fetch events from `/v1/audit/search-audit-log` and `/v2/file-events`.
*   Use `requests_toolbelt.sessions.BaseUrlSession` for HTTP transport.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.