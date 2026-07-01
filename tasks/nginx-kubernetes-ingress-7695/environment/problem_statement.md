# Feature: Report ConfigMap Keys in Telemetry

## Description

The NGINX Ingress Controller telemetry system currently doesn't track which configuration options administrators are actually using. There are two configuration maps that control the behavior of the ingress controller — the main configuration map and the management configuration map — and knowing which settings are commonly configured would provide valuable insights for prioritizing documentation, support, and feature work.

## Expected Behavior

- When the telemetry collector runs, it should read the keys present in both the main configuration map and the management configuration map.
- For each configuration map, only keys that belong to a recognized set of valid settings should be reported; unknown or unrelated keys should be filtered out.
- The reported keys should be included in the telemetry data payload so they appear in the collected telemetry output.
- When a configuration map has no recognized keys, the reported list should be absent, not an empty collection.
- The collector configuration should accept references to both the main configuration map and the management configuration map so they can be retrieved from the Kubernetes API.

## Why This Matters

Without this data, it is impossible to know which settings administrators are actually using in production. Tracking which recognized configuration keys are set — without exposing their values — provides privacy-safe usage telemetry that can guide the development roadmap and help the team focus on the most-used features.
