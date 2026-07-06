## Description

We need a new event collector integration for the Code42 insider risk platform that automatically pulls security events into Cortex XSIAM. Currently there is no automated way to ingest Code42 data — neither file activity events nor administrator audit logs — into the platform for correlation and investigation.

## Expected Behavior

- The integration should connect to the Code42 API using OAuth credentials (client ID and client secret) configured via the integration parameters.
- On each scheduled fetch, the integration should retrieve both file activity events and audit logs separately, tagging each event with its type so downstream consumers can distinguish between them.
- Events should be sent to the XSIAM event ingestion pipeline in two separate batches: one for file events and one for audit logs.
- The integration should track state between runs so it does not re-send previously ingested events, and only state entries relevant to the event types actually found should be stored.
- When events are found in a given run, a scheduling hint should be saved in the run state to trigger a faster follow-up fetch.
- Configurable limits on how many file events and how many audit events to collect per fetch cycle should be respected.
- Running the connectivity test should confirm that the API is reachable and credentials are valid, returning an "ok" result.
- A manual on-demand command should allow fetching events of a specific type (either file events or audit logs) for a given time range, returning results in both structured and human-readable form.

## Why This Matters

Security teams using Code42 for insider risk detection need their event data available in XSIAM for threat correlation, alerting, and investigation. Without this integration, they would have to manually export events from Code42, creating delays and gaps in visibility.
