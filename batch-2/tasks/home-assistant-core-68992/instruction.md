Implement a new Home Assistant integration to stream entity state changes to a cloud-hosted analytics service. Configure this integration through the UI, validating connection details and supporting ingestion modes. Ensure proper handling of entity filters, error logging, and entry unloading.

*   Define constants in `homeassistant/components/azure_data_explorer/const.py`:
    *   Include DOMAIN ('azure_data_explorer'), CONF_ADX_CLUSTER_INGEST_URI, CONF_ADX_DATABASE_NAME, CONF_ADX_TABLE_NAME, CONF_APP_REG_ID, CONF_APP_REG_SECRET, CONF_AUTHORITY_ID, CONF_SEND_INTERVAL, CONF_USE_FREE, and CONF_FILTER ('filter').

*   Implement the config flow in `homeassistant/components/azure_data_explorer/config_flow.py`:
    *   Return a FORM result with an empty errors dict initially.
    *   Strip 'https://' from CONF_ADX_CLUSTER_INGEST_URI for entry titles.
    *   Handle connection errors with {'base': 'cannot_connect'} and authentication errors with {'base': 'invalid_auth'}.
    *   Allow error recovery with successful subsequent submissions.

*   Implement `async_setup` in `homeassistant/components/azure_data_explorer/__init__.py`:
    *   Initialize `hass.data[DOMAIN]`.
    *   Store YAML filter configuration in `hass.data[DOMAIN]` under 'filter'.

*   Implement `async_setup_entry` in `homeassistant/components/azure_data_explorer/__init__.py`:
    *   Test remote cluster connection before completing setup.
    *   Handle errors by setting the entry state to ConfigEntryState.SETUP_ERROR.

*   Implement `async_unload_entry` in `homeassistant/components/azure_data_explorer/__init__.py`:
    *   Clean up listeners and scheduled tasks.
    *   Transition entry state to ConfigEntryState.NOT_LOADED after unload.

*   Implement the `AzureDataExplorer` class in `homeassistant/components/azure_data_explorer/__init__.py`:
    *   Subscribe to events, queue state changes, and send them at intervals.
    *   Filter out late events, invalid/None states, and states with newlines.
    *   Apply entity include/exclude filters.
    *   Log errors with specific messages for service and authentication failures.

*   Ensure ingestion mode handling:
    *   Use `ManagedStreamingIngestClient.ingest_from_stream` for non-free mode.
    *   Use `QueuedIngestClient.ingest_from_stream` for free mode.

*   Log specific error messages during data ingestion:
    *   'Could not find database or table' for connectivity/service errors.
    *   'Could not authenticate to Azure Data Explorer' for authentication errors.

*   Implement entity filtering:
    *   Support allowlist and denylist rules by domain, glob pattern, or entity ID.

*   Ensure `utcnow` is importable/patchable at `homeassistant.components.azure_data_explorer.utcnow`.

*   Register the domain 'azure_data_explorer' in `homeassistant/generated/config_flows.py`.

*   Update `manifest.json` in `homeassistant/components/azure_data_explorer/manifest.json`:
    *   Set domain to 'azure_data_explorer', enable config_flow, and declare dependencies on `azure-kusto-ingest` and `azure-kusto-data`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.