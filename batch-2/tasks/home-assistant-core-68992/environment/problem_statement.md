## Description

Home Assistant currently has no built-in way to stream entity state change events to a cloud-hosted time-series analytics and query service. Users who want to analyze, visualize, or run queries on their home automation data over time have no direct integration option.

## Expected Behavior

A new integration should be added that:

- Can be set up through the standard UI configuration flow, asking for cluster connection details (cluster URI, database, table, application registration ID and secret, authority ID) and an optional flag to select between ingestion modes
- Validates the connection during setup and reports appropriate errors for connectivity failures or authentication problems
- Batches and forwards state change events to the configured cloud analytics cluster at a configurable interval
- Supports two ingestion client modes: a managed streaming mode and a queued ingestion mode, selectable by the user
- Applies configurable entity filters (allowlist and denylist by domain, glob pattern, or explicit entity ID) so only relevant entities are forwarded
- Skips events that are too old, have invalid or empty state values, or contain malformed characters
- Logs meaningful error messages when the remote service or authentication fails during live operation
- Supports YAML-based configuration for filter settings, storing the filter in the integration's data store
- Properly handles entry unloading by stopping listeners and scheduled tasks

## Why This Matters

Teams and individuals who use Home Assistant and also have access to a cloud analytics platform want a first-class integration to push their device and sensor data for long-term storage and querying without needing custom scripts or workarounds.
