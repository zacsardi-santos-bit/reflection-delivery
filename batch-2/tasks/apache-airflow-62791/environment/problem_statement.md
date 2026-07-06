## Description

The Google Ads connection hook does not work with connections that are configured through the Airflow web UI's connection form. When a user fills in their Google Ads credentials using the connection form, each credential (developer token, refresh token, client ID, client secret) is stored as a separate top-level field in the connection's extras. However, the hook currently only recognizes credentials that are wrapped inside a nested object under a specific key, so connections set up through the UI always fail at runtime.

## Expected Behavior

- Connections configured via the connection form (flat/top-level credential fields) should work the same as connections using the legacy nested format.
- All hook operations — including authentication type detection, service instantiation, search queries, and listing accessible customers — should succeed with connections configured either way.
- The legacy nested format should continue to work for backward compatibility.

## Why This Matters

Users who configure their Google Ads connections through the Airflow UI get unexpected failures even though they entered all required credentials. They have to resort to manually crafting a JSON extras blob in the legacy nested format, which is error-prone and unintuitive. Both configuration paths should produce a working connection.
